# How to Upgrade to Symfony 2.8

***TIP
The video for this tutorial will be ready **very** soon!
***

**Confused by the First Paragraph? https://www.youtube.com/watch?v=K7zNY0I5JNI**

The Symfony Demo: example app: a project barely alive... on Symfony 2.7.
Ladies and gentleman, we can rebuild it, we have the technology. We have
the capability to make the world's first Symfony 3 app. Better than it was
before, stronger, faster, the 3.0 version demo app. This series is all about
upgrading to Symfony 3. 

Of course, the first step is actually to upgrade to Symfony 2.8. No wait! That's
really cool too: because it'll give us all of the features of Symfony 3.

To get to 2.8, open the `composer.json` file, find the `symfony/symfony` line and
set it to 2.8. At recording, Symfony 2.8 wasn't released yet so I'll set this
to `2.8.*@BETA`:

[[[ code('aa5ae1386f') ]]]

But you - person-living-in-the-future - you should set it to `2.8.*`. 

Head over to the terminal and run:

```bash
composer update symfony/symfony --with-dependencies
```

This will download 2.8 *and* update any other libraries that Symfony depends on.

Yes yes, I realize that we're ignoring potential updates of all of these other bundles
and libraries. But focus! Upgrade Symfony first, and *then* upgrade other libraries
when you want to.

## Changes in 2.8 (Deprecations)

Since we're just upgrading from Symfony 2.7 to 2.8, there *shouldn't* be any backwards
compatibility breaks... though one usually sneaks in... usually with forms. While
Composer is dialing out to Jordi, switch to the browser, and head to
[http://github.com/symfony/symfony](http://github.com/symfony/symfony) to check
out the upgrade log for 2.8 to see what *has* changed. 

This describes things that were deprecated. Translation: these are things you'll need
to upgrade before heading to 3.0... but you can safely ignore them for now. I've already
covered some of these in our tutorial on
[What's new in Symfony 3](https://knpuniversity.com/screencast/new-in-symfony3). For 
example, the new way you create form types has changed quite a lot. But this isn't
our concern quite yet. 

Head back to the terminal to check on our update. Cool it's done! Scroll up to see
that it updated to Symfony 2.8 Beta 1, and grabbed a whole bunch of other library
updates related to this.

Go over to the browser and refresh our localhost homepage. That's it! We've now got
the new web debug toolbar at the bottom: ooo pretty. That's your sign that you are
now upgraded to 2.8! 

That is the easiest step in this process... and it's also the most important because
Symfony 2.8 has all the features of 3.0. So yea, woo! Next we'll upgrade to the new,
fancy, Symfony 3 directory structure.
