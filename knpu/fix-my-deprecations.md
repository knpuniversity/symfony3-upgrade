# Fix My Deprecations

***TIP
The video for this tutorial will be ready **very** soon!
***

The last step is to find and eliminate the final deprecation warnings. Since the changes
to the form system are some of the biggest, navigate to a page with a form. Woh, error!

> The type name specified for the service "app.form.type.datetimepicker" does
> not match the actual name.

Because the way forms are named has changed, I know this has *something* to do with
that. The stack trace points us to `BlogController` line 149:

[[[ code('6238cecd39') ]]]

And that line points us to `PostType`. A-ha! The `publishedAt` field type is still
`app_datetimepicker`:

[[[ code('eb0f102293') ]]]

The upgrade fixer did *not* fix this type name, because it's custom. Change this to
`DateTimePickerType::class`. Then go back, hit option+enter, and click "Import class"
to add the `use` statement:

[[[ code('777247e40b') ]]]

While we're here, `BlogController` has a problem too. Instead of saying `new PostType()`,
you need to say `PostType::class`:

[[[ code('e08505a0f7') ]]]

Refresh! It works! Open the deprecation notices. There are still 3: one for the problematic
AsseticBundle, one for unquoted `@` symbols and another because the global `_self`
variable in Twig is deprecated. The `_self` usage comes from the Symfony Demo itself:
it's used in the "Show Code" functionality. Since that's specific to the Symfony
Demo, I'll ignore that one.

## Finding Unquoted @'s

So let's keep going. Open up `app/config/services.yml`. Starting in 2.8, you need
to surround any string that starts with `@` with quotes. Yea, it turns out that `@` is
a special character, so it was *always* illegal to have unquoted `@` symbols, but
the YAML parser was nice and worked anyways. Well, now the YAML parser is super strict,
so surround `@markdown` with single quotes. Do the same around `@router`:

[[[ code('2df46551b7') ]]]

And while you're here, remove the `alias` from the `form.type` tag: that's not needed
in 3.0:

[[[ code('034921cfb0') ]]]

Let's see what that did. Go back and clear the cache:

```bash
rm -rf var/cache/*
```

Refresh! Now we're down to *4* deprecations, and two are *still* coming from
unquoted YAML files. Hmm. Look at the stack trace. It's not easy to figure out *where*
this problem is coming from. But look: `CodeExplorerExtension::load()`: that class
lives in *our* code: inside the `CodeExplorerBundle`. Open that class:

[[[ code('9adf6cb449') ]]]

Inside, we load a `services.yml` file. Ah, I bet there's an `@` symbol inside! And
there it is: wrap that string in single quotes:

[[[ code('c00d26f75c') ]]]

Let's do it again: clear the cache and refresh:

```bash
rm -rf var/cache/*
```

But there's still *one* unwrapped `@` symbol. Look *really* closely. This time, you
can see that the trace starts with a `Router::getRouteCollection()` followed by a
`YamlFileLoader`. This makes me think that this is coming from a `routing.yml` file.
Open the main one. Of course! Put quotes around the `@AppBundle` string:

[[[ code('f4251fc098') ]]]

## Configuration Tweaks

Before you refresh, fix one more: the csrf notice. In this case, a config key was
renamed from `csrf_provider` to `csrf_token_generator` in `security.yml`. A lot of
changes are like this: simple renames:

[[[ code('eaef57fb8f') ]]]

Clear the cache and refresh:

```bash
rm -rf var/cache/*
```

And boom! The only deprecation left *is* coming from our code, but I'm going to ignore
it because fixing it isn't very interesting. Refresh one more time to simulate using
the cached files. We can still see the *other* problem we have because
AsseticBundle isn't yet compatible with 3.0.

In real life, this *will* block us from continuing to Symfony 3.0. Outside bundles
will be the biggest issue with upgrading. But don't worry! Until then, you have
all the features you need on 2.8. Be patient.

In your project, continue going through other sections of your site, looking for -
and fixing - more deprecated code. And of course, run your tests: if you have the
phpunit bridge installed, you'll get a print-out of deprecated calls hit from your
tests.

Once the deprecations are gone: you're ready for 3.0.
