# Upgrading to Symfony 3.0

At this point, you should've gone throughout your entire site and got rid of all of the deprecation warnings. Making your code ready to upgrade to Symfony 3. Now the only ones you maybe couldn't fix are deprecation warnings that are coming from third party libraries. There may actually be a lot of these. In our app, we actually still have a problem with something coming from the Assetic-Bundle. Okay?

At this point, before we actually upgrade to Symfony 3, we need to upgrade to this Assetic-Bundle to a version so I can actually get rid of these deprecation warnings. Let's do that. Google for Assetic-Bundle, and I'm going to click to look at the Assetic-Bundle inside of Packagist. What I want to know is what versions of Assetic-Bundle are released. It looks like the latest version is version 2.8.0.

Now, technically, we could get intelligent ... If we open our composer.json, looks like we have marked it as TILLA 2.6, but my [inaudible 00:01:31] plug-in is telling me that I have 2.7.1, which is apparently not high enough. Let's upgrade to 2.8.0, and see what happens. I'll use Carrot 2.8. That's the new preferred way to spell out your version constraints instead of the Tilde. Then I'm going to copy Symfony/Assetic-Bundle. Go back to the terminal, and our composer, update and paste just that library name because we only want to update Symfony/Assetic-Bundle.

Now for other bundles, you may not need to update to the absolute latest version. If you do a little bit more digging, you may find that you only have to upgrade that library a couple of versions in order for it to be compatible with Symfony 3. You can always upgrade to the latest version, but sometimes it'll have more changes, and you'll have to QA more things into code.

Perfect, so 2.8.0 comes down ... And those deprecation notices are actually coming from Composer, themselves, they don't have anything to do with our project.  Let's go back. Refresh this page, and those 33 deprecations are now down to 1. As a reminder, this 1 is actually a deprecation that comes from our code, this is specific to this demo project, so I'm not actually going to take time to fix it. Actually if you refresh again, you'll see no deprecations. The important thing is if we wanted to get rid of the Assetic-Bundle deprecations, we upgraded Assetic-Bundle, we did that. Life is good. We are now finally ready to upgrade our project, Symfony 3.

How do we do that? Simple, 2 step process. First, in your composer.json file, change your Symfony/Symfony Key, to 3.0.* ... Or if you're upgrading to 3.1, you can do it 3.1.*. Doesn't really matter. Once you've done that, you'll do your composer updates, but this is probably not going to work. Why not, because I'm guessing that there are a few other libraries inside of my composer.json file that are not compatible with Symfony 3.

Which means when I try to upgrade to Symfony 3, a few of these other libraries are probably going to complain and say, "Hey, I'm the version that you have specified in this file, is not compatible with Symfony 3." Differently, we're probably going to need upgrade Symfony, and a few other libraries inside of this composer.json file. Boom. We go back and see a huge, crazy, disgusting looking error, and these are difficult to read. If you scroll down here a little bit, you can see installation requires for Sensio/Distribution-Bundle requires Symfony/Process 2.2. The Sensio/Distribution-Bundle is our problem. The version we have is apparently not compatible with Symfony 3.

Here's what I do in these cases. I go over, and inside of Packagist, I search for Symfony/Symfony ... Framework Standard Edition. Next, I select a version that I'm trying to upgrade my project to. We're upgrading you 3.0, so I'm going to tag, look at the 3.0.8 tag. This is the Symfony Standard Edition, this is what you get when you start a new Symfony project. We can just look at what dependency versions it has and make sure that ours match those for any problem libraries.

Since the Sensio Distribution-Bundle was causing us problems, we can see that in the latest version of Symfony, we use Tilde 5.0. I'm going to go over and change that to Tilde 5.0. Then, it's going to be the same process over and over and over again, until you have finally upgraded all the libraries that you have, that you need to in composer.json. This might only take a few tries or it might take many tries, depending on how big your project is.

Okay, so we do have another problem. If you scroll all the way up, we can see this time, it is Sensio generator bundle is having problems. We'll skip over ... And look that that is Tilde 3.0, so let's change ours to Tilde 3.0. Once again, try the composer update. If you get this error for other libraries that are not part of the Symfony Standard Edition, then you'll need to actually go look up those libraries in Packagist and look at their latest versions just like we did with Assetic-Bundle to see what we need to upgrade to.

Voila! This time, it is actually downloading us to Symfony 3.0.8.

Congratulations, you are now on Symfony 3, and to prove it, you can go back. Refresh, and everything still works. Of course, that was a big upgrade, so definitely QA your site. Make sure you didn't miss any deprecated warnings and watch your site closely, but welcome to Symfony 3.

All right, guys, if you have any questions, let me know.

