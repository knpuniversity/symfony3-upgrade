# Upgrading to Symfony 3!

We are now finally ready to upgrade our project to Symfony 3. How do we do that?
It's a simple, 2 step process.

First, composer.json change the `symfony/symfony` key, to `3.0.*`. Or, if you're
upgrading to any even new version, use that - like `3.1.*`.

Second, run `composer update`. This time, run it with *no* arguments:

```bash
composer update
```

Yes, we technically *only* need to upgrade the `symfony/symfony` package, but since
*so* many libraries depend on this, it's pretty tough to *only* update Symfony.
If you're worried about too much stuff upgrading, make sure you version constraints
in Composer.json are *tight*.

But as we wait for this: I have a surprise! This will almost definitely not work.

## Upgrading *other* Libraries

And just as I say that, *huge* error! Oh boy, these are a pain to read at first.
But if you follow down, eventually you'll find the problem. Ah, there it is: the
installation for `sensio/distribution-bundle` requires `symfony/process` 2.2. In
normal English, Composer is saying:

> Hey! So, the version of the `sensio/distribution-bundle` package that you're requiring
> in composer.json needs `symfony/process` 2.2. You probably need to *upgrade* the
> distribution bundle to a new version that works with the 3.0 process component.

With this in mind, we need to update the version for `sensio/distribution-bundle`
to something higher. In these cases, I like to open [Packagist](packagist.org)
and search for `symfony/symfony-framework-edition`. This is the project you get
when you first download Symfony. We can chat by looking at *its* composer.json
requirements.

Let's see what it looks like at the latest 3.0 version - 3.0.8. Ok - this project
requires `sensio/distribution-bundle` at version `^5.0`. Open our `composer.json`
and change it to match: `^5.0`.

That's it! Run composer update again:

```bash
composer update
```

We *may* get another error about *another* library, but this is the process: update
composer, find the problematic package, update its version and repeat.

Yep: another problem. This time it's from `sensio/generator-bundle`. Go back to
Packagist: the version in the Standard Edition is `^3.0`. Update our `composer.json`
and update composer again:

```bash
composer update
```

If you get an error from a package that's *not* part of the Standard Edition, 
then you'll need to go look up these libraries in Packagist or on GitHub to find
a version that's 3.0-compatible. That's exactly what we did with AsseticBundle.

Et voila! We just downloaded Symfony 3.0.8.

Well, let's see if it works! Refresh! Yes! Everything still works - and we're on
the cutting edge! Of course, makes sure to run your tests and QA your site to make
sure you didn't missing anything. But, congratulations.

All right, guys, if you have any questions, let me know.

Seeya next time!
