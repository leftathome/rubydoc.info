require 'capistrano/version'
require 'rubygems'
require 'capistrano-unicorn'
require 'bundler/capistrano'
load 'deploy' if respond_to?(:namespace) # cap2 differentiator

# standard settings
set :application, "chefdoc.info"
#set :domain, "chefdoc.info"
#role :app, domain
#role :web, domain
#role :db,  domain, :primary => true

# environment settings
server "chefdoc.info", :app, :web, :db, :primary => true
set :user, "chefdoc"
set :group, "www"
set :deploy_to, "/var/www/apps/#{application}"
set :deploy_via, :remote_cache
default_run_options[:pty] = true
set :use_sudo, false

# scm settings
set :repository, "https://github.com/leftathome/chefdoc.info.git"
set :scm, "git"
set :branch, "master"
#set :git_enable_submodules, 1


namespace :deploy do
  task :restart do
    run "touch #{current_path}/tmp/restart.txt"
  end

  task :cold do
    # no migrations to run
    update_code

    run "cp #{release_path}/config/config.yaml.sample #{shared_path}/config.yaml"
    run "mkdir -p #{shared_path}/repos"
    run "git clone git://github.com/lsegal/yard.git #{shared_path}/yard"

    symlink
    restart
  end
end

namespace :chefdoc do
  task :symlink, :roles => [:app] do
    run "ln -sf #{shared_path}/config.yaml #{release_path}/config.yaml"
    run "ln -sf #{shared_path}/repos #{release_path}/repos"
    run "ln -sf #{shared_path}/pids #{release_path}/tmp/pids"
    run "ln -sf #{shared_path}/yard #{release_path}/yard"
    run "ln -sf #{shared_path}/data #{release_path}/data"
  end
end

after "deploy:create_symlink", "chefdoc:symlink"
after "deploy:restart", "unicorn:reload"
after "deploy:restart", "unicorn:restart"
