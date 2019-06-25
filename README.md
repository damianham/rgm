# rgm
Ruby Gem Manager - an alternative to gem/bundler - install gems in your project, ala NPM and Yarn

## Dude where's my gem?

Mostly for ruby projects we use bundler to manage our gems.  We can specify an install path for bundler to have the gems installed in a specific folder e.g. ***vender/bundle***
```
bundle install --path vendor/bundle
```
otherwise gems will be installed somewhere else.  We can also install gems with the ***gem*** command and specify a folder
```
$ gem install --install-dir /path/to/gems/folder foo
```

Enough with the extra typing already! show me the easy way.

### Default locations

So if I don't specify an install location where does the gem I want to use in my project get installed ?

It ***could be*** the location given by the ***GEM_HOME*** environment variable if you have set that up.
It ***could be*** a path given according to a Ruby version manager such as rbenv or rvm.  For example if I am using Ruby version 2.5.1 and rbenv ruby version manager then
```
$ gem install kaminari
```
will put that gem in ***~/.rbenv/versions/2.5.1/lib/ruby/gems/2.5.0/gems/kaminari-n.n.n***

No problem if I never want to dive into the gem source to try to find out why it isn't working for me.  But if I ***DO*** want to look at the source for the gem it becomes a real pain. First I have to find out where it is installed and then open an editor with that path.  Boring!

## Compared to NPM or Yarn

NPM and Yarn add dependency packages into the ***./node_modules*** folder in the current working directory or a global folder if the -g flag is given.
```
$ yarn add moment
```
installs the moment dependency into './node_modules/moment' and adds the dependency to the package.json file.  Need to look at the moment source ?  Expand ./node_modules/moment folder and moment.js is right there.

## Compared to Crystal

Crystal is an alternative to Ruby.  It looks like Ruby (with static typing) - but it is compiled down to an executable so is blazingly fast.  Crystal's package manager is called ***shards*** and the package dependencies for a project are configured in a ***shard.yml*** file in the project root.  The dependencies are installed with
```
$ shards install
```
and the dependencies are installed in a ***lib*** folder in the project root.  So given a dependency section in shard.yml of
```
dependencies:
  amber:
    github: amberframework/amber
    #branch: master
    version: 0.27.0
 ```
The amber package from https://github.com/amberframework/amber will be installed in ./lib/amber, making it super easy to open up the source for the Amber web framework in your code editor.

## Your Gems - right where you need them

So this is the idea behind this project.  Manage gem dependencies and by default put them right there in the project just like NPM, Yarn and shards so you can look at the source quickly and easily.

#### Some ideas about how it will work
- create an rgm command line program to manage ruby package dependencies
- maintain a global cache in ~/.rgm/gems
- clone the git project for the dependency into ~/.rgm/gems/gemname.n.n.n
- install gem dependencies into ./rgms
- symlink the repo in the global cache into ./rgms, ~/.rgm/gems/gemname.n.n.n -> ./rgms/gemname
- rgm install should take < 1 sec if all gems are in the global cache
- use git tags (rgm-package) to discover dependencies - fallback to rubygems.org
- use https://github.com/jhawthorn/pub_grub version solver for tagged github gems
- use bundler's version solver for gems from rubygems.org
- provide a shim so that require("dependency") will find the dependency in ./rgms or default gem location
- provide a cli option to fork the gem and replace the clone with your fork so you can edit and issue a PR
- only 1 version of a gem should be installed in a project, using the latest possible version number
- no need for version number in the gem folder name
- maintain package dependencies in either Gemfile or in sections in package.json if no Gemfile exists
- support scoped packages (@namespace/package)
- support github packages
  - damianham/has_generic_list
  - damianham/@repo/package_name  -> github:damianham/@repo/packages/package_name
- if multiple tagged repositories have the same package name offer user a choice
- rgm discover task to discover gems
  - use git tags to identify gems by function e.g. rgm-database, rgm-blockchain, rgm-cache etc.
```
$ rgm discover database
```
lists all gems tagged with rgm-database
```
$ rgm discover tags
```
lists all rgm tags

## See also
- [Gel](https://github.com/gel-rb/gel) - A modern gem manager
