# Vagrant build

_those people_ used a custom build pipeline outside of this repo, so we have to reverse engineer whatever is happening in here

## According to the Vagrantfile

* `scripts/setup_rvm` uses a custom PPA for rvm, but `rbenv` is actually in golang:1.20 so let's use that
* `scripts/setup_tests` uses a bunch of Awk wizardry to sniff out `required_ruby_version` from the `vagrant.gemspec`, currently at `< 3.3` which puts us on 3.1.2 according to `rbenv install --list | grep ^3`
  since the repo does not have an existing `.ruby-version` we'll create one instead of the Awk wizardry
* they then run `gem update --system` which is also safe for us since we have a "local" ruby
* they then run `bundle install`, but
  * the `Gemfile` has a reference to `hashicorp/vagrant-spec.git` riding `main`; given their rug pull,
    we will want to pin that to a non-BuSL version, but there are no tags. Thus, we just pick a `ref:`
    that is still MPL and go with that
* `gem build vagrant.gemspec`
* `gem install ./vagrant*.gem`
* `make bin/linux`

  :danger: we still need to patch, or pin, all the `hashicorp/vagrant` deps in `go.mod` so they don't drag the BuSL code into the binary

This successfully builds `bin/vagrant` and it responds successfully to `vagrant --version` (coming from `./version.txt`). After installing the `./vagrant*.gem` it then starts to respond to `vagrant version` although whining about being an unofficial installation
