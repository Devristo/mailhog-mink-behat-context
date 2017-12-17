# Mailhog Mink Behat Context [![Build Status](https://travis-ci.org/rpkamp/mailhog-mink-behat-context.svg?branch=master)](https://travis-ci.org/rpkamp/mailhog-mink-behat-context)

A simple PHP (7.1+) [Behat] context for [Mailhog][mailhog] with [Mink][mink] integration.

Allows to simulate link clicks in emails and continue from the link's URL in Mink. Suitable in browser-email-browser flows such as forgotten password resets.

This context is based on the MailhogAwareContext from [rpkamp/mailhog-behat-extension][mailhog-behat-extension].

## Installation

To install this context, run:

```bash
composer install --dev rpkamp/mailhog-mink-behat-context
```

## Usage

### Register extension in Behat

This context require `rpkamp/mailhog-behat-extension` to be enabled in your behat configuration.

If you have not already done so, add the MailhogExtension extension to your `behat.yml` like so:

```yaml
default:
  suites:
    # your suite configuration here
  extensions:
    rpkamp\Behat\MailhogExtension:
      base_url: http://localhost:8025
```

The `base_url` is the URL where the Mailhog Web UI is listening to (by default this is `http://localhost:8025).

And also include the `\rpkamp\Behat\MailhogExtension\Context\MinkAwareMailogContext` in your contexts for behat:

```yaml
default:
  suites:
    contexts:
      - rpkamp\Behat\Context\MinkAwareMailhogContext

```

Not that this context does _not_ extend `rpkamp\Behat\MailhogExtension\Context\MailhogContext` from `rpkamp/mailhog-behat-extension`, so if you need functionality from both contexts you need to include both contexts in your behat configuration.

## Gherkin step

This context contains the following gherkin step

```gherkin
When I click the link "link-description" in the last received email 
```

This step will fetch the last received email from Mailhog and search for a link with "link-description" as (in order):

- id attribute *(<a id="link-description"></a>)*
- link text *(<a>link-description</a>)*
- title attribute *(<a title="link-description"></a>)*
- alt attribute *(<a alt="link-description"></a>)*
- partial link text *(<a>some link-description</a>)*

It will stop when it finds any of the above criteria fits and that tell Mink to follow that link, so from there on you can continue in Mink. This enables scenarios like password reset where you receive an email, click a link in that email and then do something in a browser.

## Run tests

Make sure you have Mailhog running and run:

```bash
make test
```

### Running Mailhog for tests

You can either run your own instance of Mailhog or use the provided docker-compose file to run one for you.
To run Mailhog with Docker make sure you have Docker and docker-compose installed and run:

```bash
docker-compose up -d
```

### Mailhog ports for tests

To prevent port collisions with any other Mailhog instances while testing the tests expect Mailhog to listen to SMTP on port 4025 (instead of the default 1025) and to HTTP traffic on port 11025 (instead of the default 8025).

[behat]: http://behat.org/
[mailhog]: https://github.com/mailhog/MailHog
[mink]: http://mink.behat.org/en/latest/
[mailhog-behat-extension]: https://github.com/rpkamp/mailhog-behat-extension
[mailhog-behat-extension-readme]: https://github.com/rpkamp/mailhog-behat-extension/blob/master/README.md
[httplug]: https://github.com/php-http/httplug
[mailhog-client]: https://github.com/rpkamp/mailhog-client
[httplug-docs]: http://docs.php-http.org/en/latest/httplug/users.html
