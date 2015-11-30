# Directory Structure

Now that we're on Symfony 2.8 we'll want to move into the new 3.0 directory
structure which you can use on any version of Symfony. It's not mandatory, but
a good idea to stay consistent with everyone else. 

A question I frequently get about upgrade is "What do I need to change in my
project?". The answer to that is usually "nothing." You don't need to change
any of your files from one version to another, except to get rid of deprecations.

That being said, sometimes we do change the default directory structure of Symfony
and a sure fire way to find that out is to go to the Symfony Standard repository
and just compare different versions. 

If you compare 2.8 to master, you'll see all the changes that are necessary
to move into this new directory structure. 

Let's move our project into the new fancy and awesome directory structure which
we covered in the New in Symfony 3 tutorial. 

In any of our front controllers we require the `AppKernel` file, that's because
it doesn't have a namespace so it's not autoloadable by composer. A small thing 
to note is that this was changed in the default Symfony install to be autoloaded
by adding a `files` key with `app/AppKernel.php` inside of it. As soon as you have
that we can rip out the require statement in `app.php`,  `app_dev.php`, `app/console`
and in `AppCache.php`. And to make that take affect head over to the terminal
and dump the autoloader or run composer install which will also do that.

Refresh! And it still works!

The biggest change in the directory structure is the new `var` directory
which holds the cache and logs files. To get that in your project, just head
into `AppKernel` and override a couple of methods. `command+n` to pop open
the generate menu, select override and we need `getRootDir`, `getCacheDir`, 
and `getLogDir`. I'm not sure why we override `getRootDir`, because that usually  
defaults to this directory, but that's what is being done in the core so we'll
do the same and return this directory. 

For `getCacheDir`, `return dirName(__DIR__)` which represents the directory name
of the current directory which is the root directory. And then add on `.'/var/cache/'.$this->environment;`.

Do a similar thing in `getLogDir` except we'll change the file to `.'/var/logs/';`.
And that's all we need to do to move the log directories. 

In the terminal create a var directory, and then `git mv app/cache/` into `var/`.
And the same for `app/logs/` into `var/`. This might seem weird to you because the
cache and logs directories aren't normally stored in git, but we do keep a `.gitkeep`
inside of there to make sure they don't get deleted, and that's what we just moved.

Back to the browser and refresh! Yep, still working and back in our IDE we have a
new `var` directory with cache and logs inside of it. 

Next, let's look at this `bin` directory. It exists because in `composer.json`
we have a little spot down here called `"config:" "bin-dir:"`. Sometimes when you
install external libraries via composer, they'll have a binary file. And it usually
puts that binary file into a `vendor/bin` directory. That's what every PHP project
has. In Symfony we overrode that to be this `bin` directory and we didn't really
have a great reason for that. So, in newer versions we've removed that and saying,
"let's just use the same bin directory as everyone else, so it should be `vendor/bin`".

Over in the terminal go ahead and completely remove the bin directory. Except that
the `bin` directory does still exist in the Symfony 3 directory structure, it's just
used for something else. So let's go ahead and head back to our terminal and put it 
back. 

And now that it's been restored, move the console file into the `bin` directory.

That's one of the bigger changes! With `app/console` you now have to think `bin/console`.
Of course this is angry because the `bootstrap.php.cache` file is not in the same
directory. Instead of loading that, let's go with `/../app/autoload.php` because that
`bootstrap.php.cache` file itself is going to be moving. 

Over in the terminal we can run, `./bin/console`, and it works! 

Ok, moving on. About this `bootstrap.php.cache`, that now lives in the `var` directory.
But, we don't ever create this. In fact, who actually does it? And who puts it in
the `app` directory in the first place? It's actually Santa, just kidding, it's the
Sensio Distribution Bundle. Via one of these post install hooks right here. 

We need to tell the Sensio Distribution Bundle that we don't want it to like in the
app directory anymore, we want it in the var directory. To do that, add a couple
of extra configurations down here.

The first one is `"symfony-var-dir":` set to `"var"`, `"symfony-bin-dir":` set to
`"bin"` and `"symfony-tests-dir":` set to `"tests"`. The really important one here
is `symfony-var-dir`, when we run `composer install` it's going to see this and realize
that we want it to put the `bootstrap.php.cache` file into the `var` directory.

The `symfony-bin-dir` is important because of the `check.php` and `SymfonyRequirements.php`.
These have also moved in the new directory structure, and the Sensio Distribution Bundle
is also in charge of putting those inside of either the `app`, `bin` or `var` directories.
So, in a second we should see those move. 

The `symfony-test-dir`, as far as I can see that doesn't do anything, but that is in
the official Symfony standard edition, so I included it here just in case. 

Back to our favorite place, the terminal! And run, `composer install`. Nothing is
actually installing, but it does run the post install scripts. And... ding! Suddenly
a few files disappeared from the `app` directory, `check.php` and `SymfonyRequirements.php`.
`check.php` is now called `bin/symfony_requirements` and the `SymfonyRequirements` file
has been moved over to the `var` directory. All of that was done with just this little
bit of configuration here. 

In the `var` directory here there's a `bootstrap.php.cache` file so we can now delete
the original one in the `app` directory. Now that we've done that we need to go into
`app_dev.php` and include the new path to the bootstrap file. But, like the console
script, we're no longer going to include this file here, instead we'll only include
the `autoload.php` file. The reason for this is that the `bootstrap.php.cache` file
is for performance, by having a bunch of classes in one file it means that the autoloading
mechanism has to work less. But sometimes when you're debugging something it's not helpful
to see errors coming from a deep line in `bootstrap.php.cache`. 

Instead, we've decided that in the dev environment to only include the normal autoloader.
In `app.php` we'll do the same thing, but below that we'll add an `include_once` for 
`/../var/bootstrap.php.cache`. So this file does get both! Let's give that a try in the
browser. 

Hey it still works! Excellent. Now we're down to the last changes and this one involves
the `tests` directory. Before we had `tests` inside of our bundles themselves, these
have now been moved down into a new directory at the root called, `tests`. Move this
`Tests` directory into `tests`. Once it's there, rename the moved file to `AppBundle`.
The idea here is to have `tests/bundleName/individualClasses`. Now you don't have to
do this, just like any of the changes we've gone through so far, but this is the new
standard that's been set going forward. 

Now that we've moved that around we'll need to update the namespace to be `Tests/AppBundle`
in all of the tests here. The reason for this is autoloading, which we'll check out in
a second. When things are in the `src` directory, as long as we follow the namespace then
these classes can all be autoloaded. Not always, but often this is important for tests.

As soon as things are in this `tests` directory they are not going to be autoloaded because
composer doesn't know to look there. But, in `composer.json`, right below the autoload, create
a new section called `autoload-dev` and under that add a `psr-4` key for `{Tests\\}` pointing
at the `tests/` directory. With that change, as long as you follow the namespace structure
inside of this `tests` directory, then those are going to be autoloaded as well.

One other part of `tests` is this `phpunit.xml.dist` file which is normall in the `app`
directory and this is why we run `phpunit -c app` because we're pointing at `app/phpunit.xml.dist`.
To simplify things, that has been moved to the root of the project. And now that that is
moved, we need to update a few things. This here needs to of course change to `app/autoload.php`.
This here is where you're looking for the tests and you can leave these if you want, but
if you're just looking for things in the test directory this can now simply be changed
to `tests` which is pretty cool.

Lastly, uncomment out this block here and tell Symfony where your app directory is, so
change this to `app/`. This is for functional tests when Symfony tries to boot your kernel
it needs to know what directory your kernel is actually in. And that's it!

Back over in the terminal, instead of running `phpunit -c app` it's just `phpunit`.
And our tests are even passing! 

After making all of these changes our `git status` looks a little crazy, I mean it
wants to commit things to the `var` directory. We need to go through and massage our
`.gitignore` file. In the end this is going to look a little bit cleaner, which is another
bonus! 

The `var` directory is now completely things that you do not need to commit to your
repository. So, instead of ignoring individual things you can just ignore the entire
`/var/` directory. We do want to keep the `.gitkeep` file but change it to be in the
`var` directory. `app/config/parameters.yml` doesn't change, we'll want to ignore any
`phpunit.xml` file if it exists, we don't need to ignore `/bin/` any longer, remove
`composer.phar` from the list since that's usually installed globally, and we'll keep
`vendor` and `web/bundles` in here. Ah that looks much better.

Run `git status` again in the terminal. And that also looks much cleaner. 

Make one last little change in `config.yml` under the `framework` key, add `assets: ~`.
This guarantees that you can still use the assets function in twig. We don't need
this in 2.8 but in 3.0 you have to turn that on. This is actually pretty cool, 
instead of the framework bundle turning on lots of features for you, you get to
opt into the features you want available. 

Okay that is it! A brand new fancy directory structure. I really like this because
the `var` directory is really clean, the `app` directory is just configuration 
and the tests are all together for the holidays and alway. And you can do this
in any version of Symfony!

 
