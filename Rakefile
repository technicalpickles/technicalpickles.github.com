require 'rubygems'
require 'rake'
require 'jekyll/tasks'

Jekyll::Tasks.new do |jekyll|
  jekyll.rdiscount = true
  jekyll.pygments = true
end


require 'vlad'

set :domain,  'deploy@technicalpickles.com'
set :deploy_to,  '/u/sites/technicalpickles'
set :pkg_dir, '_pkg'

namespace :jekyll do
  desc "Publish Jekyll site"
  remote_task :publish do
    mkdir_p '_pkg'
    cp_r "_site", "_pkg/#{release_name}"
    wd = Dir.getwd
    begin
      Dir.chdir '_pkg'
      sh "tar jcf #{release_name}.tar.bz2 #{release_name}"
    ensure
      Dir.chdir wd
    end

    remote_tarball = "#{releases_path}/#{release_name}.tar.bz2"

    rsync "_pkg/#{release_name}.tar.bz2", releases_path
    run "tar xf #{remote_tarball} -C #{releases_path}"
    run "rm -f #{remote_tarball}"
    run "rm -f #{current_path} && ln -s #{releases_path}/#{release_name} #{current_path}"
  end
end
