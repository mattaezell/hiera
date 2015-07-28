# These two need to be loaded sperately.
# If we load packaging.rake first, and it isn't available,
# running `rake spec` in a bare repo will fail since neither
# rubygems nor `rake_task` will have loaded. However, when we load
# packaging.rake last, package building fails.
begin
  load File.join(File.dirname(__FILE__), 'ext', 'packaging', 'packaging.rake')
rescue LoadError
end

begin
  require 'rubygems'
  require 'rspec/core/rake_task'
rescue LoadError
end

Dir['tasks/**/*.rake'].each { |t| load t }

build_defs_file = 'ext/build_defaults.yaml'
if File.exist?(build_defs_file)
  begin
    require 'yaml'
    @build_defaults ||= YAML.load_file(build_defs_file)
  rescue Exception => e
    STDERR.puts "Unable to load yaml from #{build_defs_file}:"
    STDERR.puts e
  end
  @packaging_url  = @build_defaults['packaging_url']
  @packaging_repo = @build_defaults['packaging_repo']
  raise "Could not find packaging url in #{build_defs_file}" if @packaging_url.nil?
  raise "Could not find packaging repo in #{build_defs_file}" if @packaging_repo.nil?

  namespace :package do
    desc "Bootstrap packaging automation, e.g. clone into packaging repo"
    task :bootstrap do
      if File.exist?("ext/#{@packaging_repo}")
        puts "It looks like you already have ext/#{@packaging_repo}. If you don't like it, blow it away with package:implode."
      else
        cd 'ext' do
          %x{git clone #{@packaging_url}}
        end
      end
    end
    desc "Remove all cloned packaging automation"
    task :implode do
      rm_rf "ext/#{@packaging_repo}"
    end
  end
end

task :spec do
  sh %{rspec #{ENV['TEST'] || ENV['TESTS'] || 'spec'}}
end

task :test => :spec

task :default do
  sh 'rake -T'
end
