source "https://rubygems.org"

gemspec

if File.exist?(File.expand_path("../../vagrant-spec", __FILE__))
  gem 'vagrant-spec', path: "../vagrant-spec"
else
  gem 'vagrant-spec', git: "https://github.com/hashicorp/vagrant-spec.git",
    # this one is still licensed MPL
    ref: "708be5c53ea10131b19921c536eae012d1396a96"
end
