![Tests](https://github.com/php-bug-catcher/bug-catcher/actions/workflows/symfony.yml/badge.svg)

# Catch every bug in all your PHP applications in one place

<p align="center">
<img src="https://raw.githubusercontent.com/php-bug-catcher/bug-catcher/main/docs/logo/default/horizontal.svg" width="600"><br>
</p>
<img src="https://raw.githubusercontent.com/php-bug-catcher/bug-catcher/main/docs/bug_catcher_01.png" width="800" >
<img src="https://raw.githubusercontent.com/php-bug-catcher/bug-catcher/main/docs/stacktrace.png" width="800" >

## Installation

### By composer

```bash
composer create-project php-bug-catcher/skeleton your-project-name
````

### Manual

**Create blank symfony project**

```bash
composer create-project symfony/skeleton:"7.1.*" bug-catcher
```

**Add depenedencies**

```bash
composer require php-bug-catcher/bug-catcher
````

**Enable bundle if not already enabled**

```php
//config/bundles.php
return [
    ...
    BugCatcher\BugCatcherBundle::class => ['all' => true],
    ...
];
```

## Configuration

**setup packages**

```yaml
#config/packages/twig.yaml
twig:
  #...
  form_themes: [ '@EasyAdmin/symfony-form-themes/bootstrap_5_layout.html.twig' ]
  globals:
    logo: '%logo%'
    app_name: '%env(APP_NAME)%'
```

```yaml
#config/packages/twig_component.yaml
twig_component:
  #...
  defaults:
    #...
    BugCatcher\Twig\Components\: '@BugCatcher/components/'
```

```yaml
#config/packages/webpack_encore.yaml
webpack_encore:
  #...
  builds:
    bug_catcher: '%kernel.project_dir%/public/bundles/bugcatcher/'
framework:
  #...
  assets:
    packages:
      app:
        json_manifest_path: '%kernel.project_dir%/public/build/manifest.json'
      bug_catcher:
        json_manifest_path: '%kernel.project_dir%/public/bundles/bugcatcher/manifest.json'
```

**Security**

follow the instructions in the [Symfony docs](https://symfony.com/doc/current/security.html)
Modify these:

```yaml
#config/packages/security.yaml
security:
  #...
  providers:
    app_user_provider:
      entity:
        class: BugCatcher\Entity\User
        property: email
  firewalls:
    #...
    api:
      pattern: ^/api/
      stateless: true
    main:
      #...
      provider: app_user_provider
      form_login:
        login_path: bug_catcher.security.login
        check_path: bug_catcher.security.login
        enable_csrf: true
      logout:
        path: bug_catcher.security.logout
  access_control:
    - { path: ^/login$, role: PUBLIC_ACCESS }
    - { path: ^/api, roles: PUBLIC_ACCESS }
    - { path: ^/admin, roles: ROLE_ADMIN }
    - { path: ^/detail, roles: ROLE_DEVELOPER }
    - { path: ^/_components/LogList/clearAll, roles: ROLE_DEVELOPER }
    - { path: ^/_components/LogList/clearOne, roles: ROLE_DEVELOPER }
    - { path: ^/, roles: ROLE_CUSTOMER }
  role_hierarchy:
    ROLE_ADMIN: ROLE_DEVELOPER
    ROLE_DEVELOPER: ROLE_CUSTOMER
    ROLE_CUSTOMER: ROLE_USER
```

```yaml
#config/packages/doctrine.yaml
doctrine:
  orm:
    #...
    dql:
      string_functions:
        TYPE: BugCatcher\Extension\DQL\TypeFunction
```

```yaml
#config/packages/api_platform.yaml
api_platform:
  #...
  formats:
    #...
    json: [ 'application/json' ]
```

**Routes**

```yaml
#config/routes/bug_catcher.yaml
_bug_catcher:
  resource: "@BugCatcherBundle/config/routes.php"
  prefix: /
```

**Download icons**

```bash
php bin/console ux:icons:import pajamas:hamburger covid:virus-lab-research-magnifier-1 clarity:archive-line game-icons:magic-broom
```