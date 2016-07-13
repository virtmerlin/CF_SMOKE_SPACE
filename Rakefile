require 'erb'
require 'rake'
require 'rspec/core/rake_task'

RSpec::Core::RakeTask.new(:spec) do |t|
  t.pattern = Dir.glob('spec/**/*_spec.rb')
  t.rspec_opts = '--format documentation'
end

OUTPUT_FILE='manifest.yml'
TEMPLATE_FILE=OUTPUT_FILE+ '.erb'

def get_template
  File.read(TEMPLATE_FILE)
end

desc "Builds the file, using ERB."
task :gen_manifest do
  File.open(OUTPUT_FILE, "w+") do |f|
    f.write(ERB.new(get_template).result())
    f.close
  end
end

desc "Login to cloudfoundry"
task :login do
  deployment = ENV.fetch('RUBY_ENVIRONMENT', "staging")
  api_endpoint = ENV.fetch("CF_API")
  user_name = ENV.fetch("CF_USER")
  password = ENV.fetch("CF_PASS")
  test_org = ENV.fetch("CF_ORG")
  test_space = ENV.fetch("CF_SMOKE_SPACE")

  puts %x{ cf api #{api_endpoint} --skip-ssl-validation}
  puts %x{ cf login -u #{user_name} -p #{password} -o #{test_org} -s #{test_space} --skip-ssl-validation }
end

desc "logout of cloudfoundry"
task :logout do
  puts %x{ cf logout }
end

desc "Push to CF."
task :push do
  Rake::Task["gen_manifest"].invoke
  puts File.read(OUTPUT_FILE)
  Rake::Task["login"].invoke
  puts %x{ cf push }
  #Rake::Task["logout"].invoke
end

task :default => :spec
