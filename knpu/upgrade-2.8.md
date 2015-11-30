# Upgrade 2.8

The Symfony Demo: example app: a project barely alive... on Symfony 2.7.
Ladies and gentleman, we can rebuild it, we have the technology. We have
the capability to make the world's first Symfony 3 app. Better than it was
before, stronger, faster, the 3.0 version demo app. This series is all about
upgrading to Symfony 3. 

Of course, the first step is actually to upgrade to Symfony 2.8. That's really
easy, so let's do it first. Open the `composer.json` file, find the `symfony/symfony`
line. Set it to 2.8. At recording, Symfony 2.8 wasn't released yet so I'll set this
to `2.8.*@BETA`. But you should set it to `2.8.*`. 

Head over to the terminal and run `composer update symfony/symfony --with-dependencies`.
This will download 2.8 *and* update any other libraries that Symfony depends on.

We're not upgrading any other bundles or libraries right now. We *could*, but let's
focus on Symfony first.

## Changes in 2.8 (Deprecations)

Since we're just upgrading from Symfony 2.7 to 2.8, there *shouldn't* be any backwards
compatability breaks. But while this is working, switch to the browser, and
head to `http://github.com/symfony/symfony` and check out the upgrade log for 2.8
to see what has changed. 

This describes things that were deprecated, meaning things that we'll need to update
before we head to 3.0. I've already covered some of these in our tutorial on
[What's new in Symfony 3](https://knpuniversity.com/screencast/new-in-symfony3). For 
example, the new way you create form types has changed. But this isn't our concern
quite yet. 

Back to the termainal to check on our update. Cool it's done! Scroll up to see it
updated to Symfony 2.8 Beta 1, and grabbed a whole bunch of other library updates
related to this.

Over to the browser and refresh our localhost homepage. That's it! We've now got
the new  web debug toolbar at the bottom: ooo pretty. That's your sign that you are
now upgraded to 2.8! 

That is the easiest step in this process... and it's also the most important because
Symfony 2.8 has all the features of 3.0. So yea, that was easy! Next we'll upgrade
to the new Symfony 3 directory structure.
