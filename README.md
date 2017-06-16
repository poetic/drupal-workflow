# Drupal 8 Local Development and Deployment Workflow

## Requirements
- [Docker4Drupal](https://github.com/wodby/docker4drupal/). Please follow documentation for usage. There will be a section below to summary some main points.
- [Composer Template for Drupal Projects](https://github.com/drupal-composer/drupal-project)

## Workflow

Please refer to `docker-sync.yml` and `docker-compose.yml` as example. 

**UPDATE**
Please refer to [this article](https://www.drud.com/real-world-performance-gains-docker-for-mac-user-guided-cache/) to as a replacement for docker-sync. Recommend this solution because sometimes docker sync does not sync files between host and container. Please refer to docker-sync [configuration](https://github.com/EugenMayer/docker-sync/wiki/2.-Configuration) for configuration.

### Start Project
This project is based on the single site setup. Please refer to [Docker4Drupal](https://github.com/wodby/docker4drupal/) for multi site setup.

    - composer create-project drupal-composer/-drupal-project:8.x-dev some-dir --stability dev --no-interaction
    - docker-sync start
    - docker-compose up -d

Once this step is done, you can go to `http://drupal.docker.localhost:8000` to start setup and develop

**TODO**

- Run install command to skip install screen.

### Local Development

#### Drupaling and Commit

- Drupaling like you usually do!
- When you get to a stopping point, you need to export your site configuration. Run `docker-compose exec --user 82 php drush config-export -y`
- Configuration will be exported to `config/sync` directory.
- Commit and push to your repository.

#### Pull and Sync

- Git clone repo to a new site.
- Update `docker-compose.yml` to setup a new site. Refer to [Docker4Drupal](https://github.com/wodby/docker4drupal/) for instruction.
- Go through installation process.
- At this point, we should have a new blank site. What we need to do next is syncing existing configuration from repo.
- In order for us to do this, new site needs to be a clone site of existing site. To do this, we need to update new site uuid to match existing site uuid.
- At site root directory:

        - cat config/sync/system.site.yml to get uuid.
        - docker-compose exec --user 82 php drush cset "system.site" uuid "uuid-from-system-site-yml"
        - docker-compose exec --user 82 php drush config-import

- If you have this error `Entities exist of type Shortcut link and Default. These entities need to be deleted before importing.`, replace uuid in `shortcut.set.default.yml` as null then try to re-import again. Here's a link to the [bug](https://www.drupal.org/node/2583113) report in the D8 issue queue.
- After this, your site should be up-to-date.

### Deployment
**TODO**

### Docker4Drupal
**TODO**