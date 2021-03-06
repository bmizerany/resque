eval File.read('tasks/redis.rake')

$LOAD_PATH.unshift File.dirname(__FILE__) + '/lib'
require 'resque/tasks'

task :default => :test

desc "Run tests"
task :test do
  # Don't use the rake/testtask because it loads a new
  # Ruby interpreter - we want to run tests with the current
  # `rake` so our library manager still works
  Dir['test/*_test.rb'].each do |f|
    require f
  end
end

desc "Activate kicker - gem install kicker"
task :kick do
  exec "kicker -e rake lib test"
end

task :install => [ 'redis:install', 'dtach:install' ]

desc "Build a gem"
task :gem => [ :test, :gemspec, :build ]

begin
  require 'jeweler'
  $LOAD_PATH.unshift 'lib'
  require 'resque/version'

  Jeweler::Tasks.new do |gemspec|
    gemspec.name = "resque"
    gemspec.summary = ""
    gemspec.description = ""
    gemspec.email = "chris@ozmm.org"
    gemspec.homepage = "http://github.com/defunkt/resque"
    gemspec.authors = ["Chris Wanstrath"]
    gemspec.version = Resque::Version 
  end
rescue LoadError
  puts "Jeweler not available. Install it with: "
  puts "gem install jeweler"
end

begin
  require 'sdoc_helpers'
rescue LoadError
  puts "sdoc support not enabled. Please gem install sdoc-helpers."
end

desc "Push a new version to Gemcutter"
task :publish => [ :test, :gemspec, :build ] do
  system "git tag v#{Resque::Version}"
  system "git push origin v#{Resque::Version}"
  system "git push origin master"
  system "gem push pkg/resque-#{Resque::Version}.gem"
  system "git clean -fd"
  exec "rake pages"
end
