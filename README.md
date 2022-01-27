# Sitegeist.LostInTranslation
## Automatic Translations for Neos via DeeplApi

Documents and Contents are translated automatically once editors choose to "create and copy" a version in another language.
The included DeeplService can be used for other purposes aswell.

The development was a collaboration of Sitegeist and Code Q.

### Authors & Sponsors

* Martin Ficzel - ficzel@sitegeist.de
* Felix Gradinaru - fg@codeq.at

*The development and the public-releases of this package is generously sponsored
by our employers http://www.sitegeist.de and http://www.codeq.at.*

## Installation

Sitegeist.LostInTranslation is available via packagist. Run `composer require sitegeist/lostintranslation`.

We use semantic-versioning so every breaking change will increase the major-version number.

## How it works

By default all inline editable properties are translated using DeepL (see Setting `translateInlineEditables`).
To include other `string` properties into the automatic translation the `options.translate: true`
can be used in the property configuration. Also, you can disable automatic translation in general for certain node types
by setting `options.automaticallyTranslate: false`.

Some very common fields from `Neos.Neos:Document` are already configured to do so by default.

```yaml
'Neos.Neos:Document':
  options:
    automaticallyTranslate: true
  properties:
    title:
      options:
        automaticallyTranslate: true
    titleOverride:
      options:
        automaticallyTranslate: true
    metaDescription:
      options:
        automaticallyTranslate: true
    metaKeywords:
      options:
        automaticallyTranslate: true
```

Also, automatic translation for `Neos.Neos:Content` is enabled by default:

```yaml
'Neos.Neos:Content':
  options:
    automaticallyTranslate: true
```

## Configuration

This package needs an authenticationKey for the DeeplL Api from https://www.deepl.com/pro-api.
There are free plans that support a limited number but for productive use we recommend using a payed plan.

```yaml
Sitegeist:
  LostInTranslation:
    DeepLApi:
      authenticationKey: '.........................'
```

The translation of nodes can is configured via settings:

```yaml
Sitegeist:
  LostInTranslation:
    nodeTranslation:
      #
      # Enable the automatic translations of nodes while they are adopted to another dimension
      #
      enabled: true

      #
      # There are two strategies:
      # (*) once: will translate the node once the editor switches the language dimension and only then
      # (*) sync: will translate and sync the node anytime the base language has been edited
      #
      strategy: 'once'

      #
      # Translate all inline editable fields without further configuration.
      #
      # If this is disabled iline editables can be configured for translation by setting
      # `options.translateOnAdoption: true` for each property seperatly
      #
      translateInlineEditables: true

      #
      # The name of the language dimension. Usually needs no modification
      #
      languageDimensionName: 'language'

      #
      # When strategy "sync" is selected, this setting defines which languages
      # to translate to which other languages
      #
      # This example would sync and translate all nodes in language "de" to "it" and "en"
      #
      # translationMapping:
      #   de:
      #     - 'it'
      #     - 'en'
      #
      translationMapping: [ ]
```

To enable automated translations for a language preset, just set `options.translationStrategy` to  `once` or `sync`.

* `once` will translate the node only once when the editor switches the language in the backend while editing this node. This is useful if you want to get an initial translation, but work on the different variants on your own after that.
* `sync` will translate and sync the node every time the node in the default language is published. Thus, it will not make sense to edit the node variant in an automatically translated language using this options, as your changed will be overwritten every time.

If a preset of the language dimension uses a locale identifier that is not compatible with DeepL the deeplLanguage can
be configured explicitly for this preset via `options.deeplLanguage`.

```yaml
Neos:
  ContentRepository:
    contentDimensions:
      'language':
        presets:

          #
          # Danish uses a different locale identifier then DeepL so the `deeplLanguage` has to be configured explicitly
          #
          'dk':
            label: 'Dansk'
            values: ['dk']
            uriSegment: 'dk'
            options:
              deeplLanguage: 'da'
              translationStrategy: 'once'

          #
          # The bavarian language is not supported by DeepL and is disabled
          #
          'de_bar':
            label: 'Bayrisch'
            values: ['de_bar','de']
            uriSegment: 'de_bar'
            options:
              translationStrategy: 'sync'
```
## Performance

For every translated node a single request is made to the DeepL API. This can lead to significant delay when Documents with lots of nodes are translated. It is likely that future versions will improve this.

## Contribution

We will gladly accept contributions. Please send us pull requests.

## Changelog

### 2.0.0

* The preset option `translationStrategy` was introduced. There are now two auto-translation strategies
  * Strategy `once` will auto-translate the node once "on adoption", i.e. the editor switches to a different language dimension
  * Strategy `sync` will auto-translate and sync the node according to the `translationMapping` setting every time a node is updated in the source language
* The node setting `options.translateOnAdoption` as been renamed to `options.automaticallyTranslate`
* The new node option `automaticallyTranslate` was introduced
