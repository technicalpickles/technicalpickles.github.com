require 'rubygems'
require 'rake'
require 'jekyll/tasks'

Jekyll::Tasks.new do |jekyll|
  jekyll.rdiscount = true
  jekyll.pygments = true
end


def current_release
  Dir["pkg/*"].map {|f| File.basename(f) }.select {|f| f =~ /^\d*$/}.sort.last
end

pkg_path = '_pkg'
server_user = 'deploy'
server_host = 'technicalpickles.com'
deploy_to = '/u/sites/technicalpickles'
current_path = File.join(deploy_to, 'current')
releases_path = File.join(deploy_to, 'releases')

def ssh(server_user, server_host, command)
  full_command = "ssh #{server_user}@#{server_host} #{command}"
  puts full_command
  sh full_command
end

task :package do
  mkdir_p pkg_path

  datestamp = Time.now.utc.strftime("%Y%m%d%H%M%S")
  cp_r '_site', "#{pkg_path}/#{datestamp}"

  wd = Dir.getwd
  begin
    Dir.chdir pkg_path
    sh "tar jcf #{datestamp}.tar.bz2 #{datestamp}"
  ensure
    Dir.chdir wd
  end
end

task :release do
  tarball = "#{current_release}.tar.bz2"
  sh "scp #{pkg_path}/#{tarball} #{server_user}@#{server_host}:#{releases_path}"
  ssh server_user, server_host, "tar xf #{releases_path}/#{tarball} -C #{releases_path}"
  ssh server_user, server_host, "rm -f #{current_path}"
  ssh server_user, server_host, "ln -sf #{releases_path}/#{current_release} #{current_path}"
end
