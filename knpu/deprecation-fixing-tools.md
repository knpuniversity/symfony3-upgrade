# Deprecation Fixing Tools

***TIP
The video for this tutorial will be ready **very** soon!
***

Ideally, your outside bundles are no longer triggering deprecated warnings. Now it's
time to update *our* code for Symfony 3. This means finding deprecation warnings,
fixing them everywhere you can think of, and, well, repeating! It's pretty simple,
but sometimes the true source of a deprecation can be tricky to find.

## Symfony Upgrade Fixer

Fortunately, there are 2 tools to help us. Google for [Symfony Upgrade Fixer](https://github.com/umpirsky/Symfony-Upgrade-Fixer).
This is a sweet tool made by my good friend Saša. It tries to find deprecated code
and fix it *for* you. How nice!? It only supports a few things, but it aims to fix
the most *annoying* ones, like form changes.

Copy the `wget` line and paste it into your command line. Or just use that URL to
download the file manually. Copy the second permissions line to make that file
executable. Cool! Now we have a `symfony-upgrade-fixer` utility! Run it!

```bash
symfony-upgrade-fixer
```

And then actually *use* it with fix and then `.` to fix this directory:

```bash
symfony-upgrade-fixer fix .
```

Behind the scenes, that's snooping through your project and writing new code for you.
Ding! It fixed two form classes:

[[[ code('5d32ce898b') ]]]

[[[ code('bd25ded46f') ]]]

By using `git diff`, I can see that it updated to the new
[TextareaType::class](https://knpuniversity.com/screencast/new-in-symfony3/form-updates)
format. If you have a lot of forms, this is *awesome*.

## Symfony Deprecation Detector

This tool caught a *few* things, but there's a lot more. So... let's try another tool!
Google for [Symfony Deprecation Detector](https://github.com/sensiolabs-de/deprecation-detector).
This tool doesn't fix your project, but it detects more things than the first one.

Copy the clone command and run it in the terminal:

```bash
cd any/path
git clone git@github.com:sensiolabs-de/deprecation-detector.git
```

Move into the directory and install the composer libraries:

```bash
cd deprecation-detector
composer install
```

This will give us a new binary that we can use on our project. Switch back into
the directory of *our* project, then run the `bin/deprecation-detector` command from
that project:

```bash
/path/to/deprecation-detector/bin/deprecation-detector
```

Boom! This correctly sees that 3 form types still have a `getName()` method, which
they should *not* have anymore. Go into `AppBundle/Form`, open up each form type,
and remove the `getName()` method from the bottom.

Perfect! Try the command again:

```bash
/path/to/deprecation-detector/bin/deprecation-detector
```

No violations! I *wish* this meant that you had no more deprecated features, but
it's an imperfect tool. We need to hunt for the last few deprecations.
