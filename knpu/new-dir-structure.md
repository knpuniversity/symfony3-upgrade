# Upgrading to the Symfony 3.0 Directory Structure

Hey, we're on Symfony 2.8! Before we *fully* upgrade, let's move into the new 3.0
directory structure... which you can actually use with any version of Symfony...
But in 3.0, it's super official. And remember: the new structure isn't required.
Want to keep things how they are now? Do it! We talk more about the new structure
in our [What's new in Symfony 3](https://knpuniversity.com/screencast/new-in-symfony3/new-dir-structure)
screencast.

People often ask me:

> What are the first 10 digits of pi?

Great question: they're 3.1415926535. They also ask me:

> What do I need to change in my code after upgrading?

The answer to that is... nothing! Ya know, because Symfony doesn't break backwards
compatibility.

But we *do* sometimes tweak things in the default directory structure of Symfony.
A sure-fire way to find out about those changes is to go to the Symfony Standard
repository and just compare the versions. If you
[compare 2.8 to master](https://github.com/symfony/symfony-standard/compare/2.8...master),
you'll see all the changes needed to move into this new directory structure.

Ready to do this?

## Autoload AppKernel

In each front controller we require the `AppKernel` file:

[[[ code('a0b2dc920d') ]]]

That's because this file doesn't have a namespace and doesn't live in `src`. Basically,
it's weird, and can't be autoloaded.

That was lame, so it *is* autoloaded now! In `composer.json`, add a `files` key with
`app/AppKernel.php` inside of it:

[[[ code('0c04f10f80') ]]]

With that, we can rip out the require statement in `app.php`,  `app_dev.php`, `app/console`
and in `AppCache.php`. Nice!

For this to take effect, head over to the terminal and dump the autoloader:

```bash
composer dump-autoload
```

Running `composer install` also does this.

Refresh! And it still works!

## The var/ Directory

The most noticeable change is the new `var/` directory which holds stuff you don't
need to modify, like  the `cache` and `logs`. To update your project, open `AppKernel`
and override a few methods. I'll use `command+n` to open the generate menu. Select
override and choose `getRootDir()`, `getCacheDir()`, and `getLogDir()`. I'm not sure
why we override `getRootDir()`, because that defaults to *this* directory, but I
*do* like that it looks less magic now:

[[[ code('4a683e16b8') ]]]

For `getCacheDir()`, `return dirName(__DIR__)` - that looks in `app/` then goes up
to the root - and then add `.'/var/cache/'.$this->environment;`:

[[[ code('264c9f3d15') ]]]

Do a similar thing in `getLogDir()` except change to `.'/var/logs/';`:

[[[ code('acf7df141d') ]]]

That's *all* you need to do to move the cache & logs directories. 

In the terminal create a `var/` directory, and then `git mv app/cache/` into `var/`.
Do the same to move `app/logs/` into `var/`:

```bash
mkdir var
git mv app/cache var/
git mv app/logs var/
```

This might seem weird to you because the cache and logs directories aren't normally
stored in git. But we *do* keep a `.gitkeep` file inside each to make sure they don't
get deleted. That's what we just moved.

Back to the browser and refresh! It's still alive! In our IDE, we have a new `var/`
directory that's happily populated with cache and logs files. 

## Changes to bin/

Next, look at the `bin` directory. It exists because `composer.json` holds a little
bit of configuration: `"config:" "bin-dir:"`:

[[[ code('6c9b518ab3') ]]]

Sometimes when you install an external library via composer, it comes with a binary
file. Composer usually puts that into a `vendor/bin` directory for convenience. With
this config, it goes into `bin/` instead?

Why did we override this? I have no idea! Composer was young, it was the wild-west,
things happen. And really, it's not a big deal, except that it makes Symfony projects
look a bit "weird" compared to others. Remove this config so that Composer uses
the normal `vendor/bin`.

In the terminal, completely remove the `bin/` directory. But wait! The `bin` directory
*does* still exist in the Symfony 3 directory structure, it just has a new job.
Put it back!

```bash
rm -rf bin
mkdir bin
```

## bin/console

Phew! Now that it's back, move the `app/console` file into `bin/`:

```bash
mv app/console bin/console
```

This is one of the biggest changes. Goodbye `app/console`, hello `bin/console`.
Of course this file is angry because the `bootstrap.php.cache` file is not in the same
directory anymore. But instead of loading it, load `/../app/autoload.php` instead:

[[[ code('7bad0be324') ]]]

Why? Well, in part because `bootstrap.php.cache` is going to move too.

Head to the terminal and try out your very first `bin/console`:

```bash
bin/console
```

It's still happy, and I'm still happy.

## Moving bootstrap.php.cache

Speaking of `bootstrap.php.cache`, this now lives in the `var` directory. But wait,
*we* aren't responsible for this file. In fact, who created this in the first place?
Who put this in the `app` directory to begin with? It was Santa! Just kidding: it's
the SensioDistributionBundle, via one of these post install hooks in `composer.json`:

[[[ code('2fa9eeec21') ]]]

We need to tell the SensioDistributionBundle that we want it to be built in `var`
instead of `app`. To do that, add a couple of new configuration lines in the `extra`
section of `composer.json`.

Add `"symfony-var-dir":` set to `"var"`, `"symfony-bin-dir":` set to `"bin"` and
`"symfony-tests-dir":` set to `"tests"`. The really important one here `symfony-var-dir`:

[[[ code('b3fe2a32b3') ]]]

When you run `composer install` it's going to see this and realize that you want
it to put the `bootstrap.php.cache` file into the `var` directory. It's as simple
as that.

The `symfony-bin-dir` is *also* important because of the `check.php` and `SymfonyRequirements.php`
files. These also moved, and the SensioDistributionBundle is *also* in charge of
putting those inside of either the `app`, `bin` or `var` directories. In a second,
you'll see these move.

The `symfony-test-dir`, well, as far as I can see, that doesn't do anything. But
it exists in the official Symfony Standard Edition and darn it, I'm a rule-follower.

Back to our favorite place, the terminal! Run:

```bash
composer install
```

Nothing is installing, but it does run the post install scripts. And... ding! Suddenly
a few files disappeared from the `app` directory: `check.php` and `SymfonyRequirements.php`.
`check.php` is now called `bin/symfony_requirements` and the `SymfonyRequirements` file
has been moved over to the `var` directory. All of that was done thanks to that extra
configuration.

## Require autoload.php instead of bootstrap.php.cache

In the `var` directory, there's the `bootstrap.php.cache` file. Delete the original
one in `app`. To un-break our application, we need to go into `app_dev.php` and require
the new path to the bootstrap file. But stop! Like the `console` file, we're no longer
going to include `bootstrap.php.cache`. Instead require `app/autoload.php`:

[[[ code('bebf25f79c') ]]]

***TIP
Actually, the `require_once` was also changed to `require`, which guarantees that
the `$loader` is returned.
***

Why? Well, the *whole* point of `bootstrap.php.cache` is performance: by having a
bunch of classes in one file, it means that the autoloading mechanism has to work
less. But when you're debugging, it's not helpful to see errors coming from a deep
line in `bootstrap.php.cache`.

Instead, in the `dev` environment, only include the normal autoloader. In `app.php`
do the same thing. But since performance is totally rad to have in the `prod` environment,
add an `include_once` below that for `/../var/bootstrap.php.cache`:

[[[ code('905387dbe3') ]]]

Let's give that a try in the browser. 

And it *still* works. I can't seem to break our app.

## Moving Tests

We're now down to the last change: it involves the `tests` directory. Wait, you
don't write tests? You are a brave, brave developer.

For the rest of you law-abiding citizens. the tests *used* to live inside of the
bundles themselves. These have now been moved down into a new root directory called,
well, `tests`! Move the `AppBundle/Tests` directory into `tests`. Once it's there,
rename it to `AppBundle` so that you have a nice `tests/AppBundle`.

The idea is to have paths like `tests/bundleName/individualClasses`. There's no technical
reason for this change, it's just a new standard.

With everything moved around, you'll need to update the namespace of each class to
be `Tests/AppBundle`:

[[[ code('d7517c2845') ]]]

The reason for this is autoloading: you know, the old "your directory structure must
match your namespace" thing. You can sometimes get away with messing this up with
tests... but not always.

This is sweet... except that Composer doesn't know to look in the `tests/` directory.
Open `composer.json`. Below `autoload`, create a new section called `autoload-dev`.
Below it add a `psr-4` key for `{ "Tests\\": }` pointing at the `tests/` directory:

[[[ code('e1f05852a2') ]]]

We're good!

## Moving phpunit.xml.dist

On the topic of tests, the `phpunit.xml.dist` file *normally* lives in the `app/`
directory. That's why you run `phpunit -c app`.

To simplify things, that has been moved to the root of the project:

```bash
mv app/phpunit.xml.dist .
```

After moving it, we need to update a few things. Update `bootstrap` to `app/autoload.php`.
In the `testsuites` section, this is how phpunit know *where* tests live.
This can now simply be changed to `tests`... which is pretty cool:

[[[ code('ffdf6612d1') ]]]

Finally, uncomment out the `php` block and tell Symfony where your app directory
is by changing the `KERNEL_DIR` value to `app/`:

[[[ code('f8a65a7c6e') ]]]

This is for functional tests: when Symfony tries to boot your kernel, it needs to
know... where you kernel lives!

Get back to the terminal. But don't run `phpunit -c app`, that's *old* news. Just
run:

```bash
phpunit
```

And hey, our tests are even passing! 

## Changes to .gitignore

After making all of these changes, `git status` looks a little crazy:

```bash
git status
```

Open up `.gitignore`. Ok, this guy is totally out of date now. Let's help him out.

The `var` directory now completely holds things that you do *not* need to commit
to your repository. So, instead of ignoring individual things, ignore the entire
`/var/` directory. We *do* want to keep the `.gitkeep` file in `cache` and `logs`,
but change each to start with `/var/`. The `app/config/parameters.yml` file doesn't
change and now we need to ignore any `phpunit.xml` file if it exists. We don't need
to ignore `/bin/` any longer and I'll remove `composer.phar` from the list since
that's usually installed globally. Keep `vendor` and `web/bundles` at the bottom:

[[[ code('c1ebafbbaa') ]]]

Ah that looks much better.

Run `git status` again in the terminal:

```bash
git status
```

SO much better.

## Tiny Configuration Changes

Make one last little change in `config.yml`. Under the `framework` key, add `assets: ~`:

[[[ code('ceec63ba9f') ]]]

This guarantees that you can still use the `asset()` function in twig. We don't need
this in 2.8 but in 3.0 you have to turn that on explicitly. This is actually pretty
cool: instead of the framework bundle turning on a lot of features automatically,
you get to opt into the features you want available. 

Okay that is it! Enjoy your brand new fancy directory structure. I really like it
because the `var` directory is stuff I don't need to touch, the `app` directory is
just configuration and the `tests/` are all together for the holidays.
