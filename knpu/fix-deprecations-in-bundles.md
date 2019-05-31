# Fix Deprecation Warnings from Bundles

Once you're on 2.8, there's a new game: find and fix deprecation notices. But there's
a catch! You won't hit *all* of your code paths at runtime. Some code paths are only
executed when the cache is being built. To hit those, start by clearing the cache:

```bash
rm -rf var/cache/*
```

Refresh the page and click into the deprecated calls on the Web Debug Toolbar.

## Deprecations come from Outside Bundles

Ok, here's the *really* tricky thing about deprecation notices: many of them are
*not your fault*. Yep, a lot of notices come from all of those lazy third-party bundles
you're using in your project. So to start, let's find out what outside bundles are
causing problems... before we worry about fixing our stuff.

The first deprecation - about not quoting `@` symbols in YAML is *our* fault.

> Not quoting a scalar starting with '@' is deprecated since Symfony 2.8

But it's not easy to see: you need to study the stack trace. This ultimately starts
with `AppKernel::registerContainerConfiguration()` where *our* configuration files are
loaded.

The second is complaining about bad configuration in `security.yml`: that's *also*
our fault, and we'll fix it in a minute.

But look at the third warning:

> The Symfony\Component\DependencyInjection\Reference::isStrict method is
> deprecated since version 2.8

Look closely at the `LoggerChannelPass`: that's coming from MonologBundle. That's
the first outside bundle that needs to be updated.

Below that, the `knp_pagination` Twig extension problem is obviously coming from
KnpPaginatorBundle.

But before you upgrade those, go back and refresh again. This time the page pulls
from the cache, and that can sometimes cause different deprecation notices to show
up. A-ha!

> The class "Symfony\Bundle\AsseticBundle\Config\AsseticResource" is performing
> resource checking through ResourceInterface::isFresh(), which is deprecated
> since 2.8

This deprecation warning comes from AsseticBundle: that's our *third* troublesome
library.

## Updating Bundles with Deprecations

Update these by running `composer update` followed by their names. Copy them from
`composer.json`:

```bash
composer update knplabs/knp-paginator-bundle symfony/assetic-bundle symfony/monolog-bundle --with-dependencies
```

Notice I'm *not* tweaking their version constraints in `composer.json`. Maybe I *will*
need to do this, but I'll take the lazy route first and hope that upgrading these
to the newest version allowed by their existing constraints will be enough.

Let's see what happens!

Cool! This downloaded some patch version updates, which *may* have solved our problem...
or maybe it didn't. Clear the cache and go refresh:

```bash
rm -rf var/cache/*
```

Click into the deprecations. Now the notices are down to 5 and they're coming from
*our* code. Problem solved! But wait, refresh again. Huh, now there are 32 notices:
the ones from AsseticBundle are back! The new version of AsseticBundle did *not*
fix that problem.

## The Bundle isn't Symfony 3 Ready!?

Google for AsseticBundle and go to its [Github Page](https://github.com/symfony/assetic-bundle).
The first thing to look for is a release that has Symfony 3 support. Huh, there *is*
one that claims support: version 2.7.1. And that *is* the version of the library
we just downloaded. Usually, this means you're fine... but *clearly* it's not fine:
we're still getting deprecated notices! What's going on AsseticBundle!

In fact, at the time of recording, this bundle claims Symfony 3 support, but it doesn't
*quite* have it: there's one pull request that still needs to be merged. By the time
you're watching this, it'll hopefully be merged and you'll happily get the 2.7.2
version.

But I'm glad this happened: it uncovers the most difficult part of upgrading to
Symfony 3. If you use a lot of outside bundles, they might not all be ready immediately
when Symfony 3 is released. In fact, some might not be updated a month later. Your
job is to check the repository, see if there is a Symfony 3-compatible release, and
open up a friendly issue if there isn't.

For right now, until this is merged, there's nothing we can do. So let's ignore these
deprecations and fix everything else.
