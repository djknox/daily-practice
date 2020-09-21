## Daily Practice
### Monday, September 21st, 2020
---


# [Laravel Explained](https://laracasts.com/series/laravel-explained)  


## [Upgrade the Laracasts Codebase to Laravel 8](https://laracasts.com/series/whats-new-in-laravel-8/episodes/11)

Laravel 8 Upgrade Guide: https://laravel.com/docs/8.x/upgrade



# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Restrict Access With Nginx](https://laracasts.com/series/how-do-i/episodes/1)

- ssh into server
- edit `/etc/nginx/sites-available/the-name-of-your-site`:
   * add commands to `server` block
   * `deny all;`
   * `allow 104.128.167.138;`
   * or using http basic authentication:
        ```
        auth_basic "Admin Only";    // use basic authentication, title of modal will be "Admin Only"
        auth_basic_user_file /etc/nginx/.htpasswd;   // credentials file is at /etc/nginx/.htpasswd
        ```
      - will need `htpasswd` utility: `sudo apt-get install apache2-utils`
      - create credentials file with new user: `sudo htpasswd -c /etc/nginx/.htpasswd name-of-user`
- reload the config file: `sudo service nginx reload`



## [How Do I Distribute an NPM Package Written With ES2015](https://laracasts.com/series/how-do-i/episodes/2)

If you're distributing a package through NPM, how to write ES2015 code that can work everywhere?
- Need compilation process that converts EcmaScript into vanilla JavaScript.

Packages:
- `babel-cli`
- `babel-preset-es2015`

Move files into a directory, such as `src`, that can be targeted for compilation

Use Babel, with es2015 preset, to `dist` destination directory and from `src` source directory
- `babel --presets=es2015 -d dist src`

Compiling everything to `dist`, so need to update the entry-point (file that is triggered when package is imported):
- in `package.json`:
    ```
    {
        "main": "index.js",     // update to "dist/index.js"
    }
    ```

Add `src/` to `.npmignore`: `src/` will be excluded from being published to npm  
Add `dist/` to `gitignore`: `dist/` will be excluded when committing with git  
