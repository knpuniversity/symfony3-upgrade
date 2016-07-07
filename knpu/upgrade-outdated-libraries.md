# Upgrade Outdated Libraries!

If you've done your homework, then at this point, you've gone through your entire
site and updated your code to remove all of the deprecation warnings.

## Upgrading Offending Libraries/Bundles!

But, not *all* the deprecation warnings are coming from *our* code - it's pretty
likely that you have a group of external libraries and bundles that are *also*
using deprecated code.

In our app, we have exactly this problem, with AsseticBundle. So before we upgrade
to Symfony 3, we need to upgrade to a new version of this library that does *not*
use old code.

No problem! Google for AsseticBundle. I'll click into the link on Packagist. I'm
looking here to find the latest released version of Packagist, which right now, is
2.8.0. If we update to that, those deprecation warnings will *hopefully* go away.

Open up `composer.json`. Our version is `~2.6`, but my PhpStorm Composer plugin tells
me that we actually have 2.7.1. Let's require `2.8.0` by changing this to `^2.8`.
The `^` symbol is the new hipster way to to describe your version constraints - it
basically allows 2.8 and higher, but not some future 3.0 version of the bundle.

Next, copy the library name - `symfony/assetic-bundle`. Since we *only* want to
upgrade this library right now, find your terminal and run:

```bash
composer update symfony/assetic-bundle
```

### What Version to Choose?

Now, if a library is giving you deprecation warnings, you *do* need to upgrade it,
but you may *not* need to upgrade it to the absolute latest version. If you want
to be a bit more conservative, you *can* dig into the release notes of a library
to find the *first* version number that is Symfony 3 compatible. It's more work,
but might mean less QA time than upgrading a library a lot of versions at once.

Hello AsseticBundle 2.8.0! Oh, and see those messages from Composer? That's another
usage of deprecations in action: some of our code is using some deprecated functionality
from Composer itself. Those will probably go away when we upgrade Symfony. But, it's
not important right now.

Let's see if those deprecations are gone: refresh the page. Yes! 33 deprecations
down to *one*. And don't worry about that last deprecation - it's very specific
to this project - so I'm not going to take time to fix it.

We've now upgraded all outside libraries that were not Symfony 3 compatible. Let's
upgrade!
