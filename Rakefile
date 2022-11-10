#require 'fileutils'
#include FileUtils
require 'rake'

# The install task was stolen from RyanB
# http://github.com/ryanb/dotfiles

desc "install the snippets and support_functions.vim into user's vim directory @home"
task :install => ["snippets_dir:find"] do
  replace_all = false
  Dir['*'].each do |file|
    next if %w[Rakefile].include? file

    if File.exist?(File.join(@snippets_dir, "#{file}"))
      if replace_all
        replace_file(file, @snippets_dir)
      else
        write_file(file,@pathogen_dir)
      end
    else
      link_file(file, @snippets_dir)
    end
  end
  if File.exist?(File.join(@pathogen_dir, "support_functions.vim"))
    replace_file(file, @pathogen_dir)
  else
    write(file, @pathogen_dir)
  end
end

def write(file, dir)
  print "overwrite #{File.join(dir, file)}? [ynaq] "
  case $stdin.gets.chomp
  when 'a'
    replace_all = true
    replace_file(file, dir)
  when 'y'
    replace_file(file, dir)
  when 'q'
    exit
  else
    puts File.join(dir, "#{file}")
  end
end

def replace_file(file, dir)
  link_file(file, dir)
end

def link_file(file, dir)
  puts "linking #{path_to(file, dir)}"
  ln_s File.join(FileUtils.pwd,file), path_to(file, dir), :force => true
end

def path_to(file, dir)
	File.join(dir, "#{file}")
end


namespace :snippets_dir do
	desc "Sets @snippets_dir dependent on which OS You run"
  task :find do
    vim_dir = File.join(ENV['VIMFILES'] || ENV['HOME'] || ENV['USERPROFILE'], RUBY_PLATFORM =~ /mswin|msys|mingw32/ ? "vimfiles" : ".vim")
    @pathogen_dir = File.join(vim_dir, "bundle")
    @snippets_dir = File.directory?(@pathogen_dir) ? File.join(@pathogen_dir, "snipmate", "snippets") : File.join(vim_dir, "snippets")
  end

  desc "Purge the contents of the vim snippets directory"
  task :purge => ["snippets_dir:find"] do
    rm_rf @snippets_dir, :verbose => true if File.directory? @snippets_dir
    mkdir @snippets_dir, :verbose => true
  end
end

desc "Copy the snippets directories into ~/.vim/snippets"
task :deploy_local => ["snippets_dir:purge"] do
  Dir.foreach(".") do |f|
    cp_r f, @snippets_dir, :verbose => true if File.directory?(f) && f =~ /^[^\.]/
  end
  cp "support_functions.vim", @snippets_dir, :verbose => true
end

desc "Alias for purge"
task :purge => ["snippets_dir:purge"]

desc "Alias for default task run easy 'rake'"
task :default => :install
