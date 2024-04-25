# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) { |repo_name| "https://github.com/#{repo_name}" }

group :jekyll_plugins do
    gem "jekyll"
    gem 'jekyll-feed'
    gem 'jekyll-readme-index'
    gem 'jemoji'
    gem 'webrick'
    gem "jekyll-sass-converter", "~> 3.0.0"
    gem "jekyll-seo-tag"
    gem "jekyll-sitemap"
    gem "jekyll-relative-links"
    gem "jekyll-redirect-from", "~> 0.16.0"
    gem "sass-embedded", "~> 1.69.5"
    gem "google-protobuf", "~> 3.24.4"
    # gem "rails"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
platforms :mingw, :x64_mingw, :mswin, :jruby do
    gem "tzinfo", ">= 1", "< 3"
    gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
