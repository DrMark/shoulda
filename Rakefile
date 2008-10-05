require 'rake'
require 'rake/testtask'
require 'rake/rdoctask'
require 'rake/gempackagetask'
require 'lib/shoulda/context'
load 'tasks/shoulda.rake'

# Test::Unit::UI::VERBOSE
test_files_pattern = 'test/{unit,functional,other}/**/*_test.rb'
Rake::TestTask.new do |t|
  t.libs << 'lib'
  t.pattern = test_files_pattern
  t.verbose = false
end

Rake::RDocTask.new { |rdoc|
  rdoc.rdoc_dir = 'doc'
  rdoc.title    = "Shoulda -- Making tests easy on the fingers and eyes"
  rdoc.options << '--line-numbers' << '--inline-source'
  rdoc.template = "#{ENV['template']}.rb" if ENV['template']
  rdoc.rdoc_files.include('README.rdoc', 'CONTRIBUTION_GUIDELINES.rdoc', 'lib/**/*.rb')
}

desc "Run code-coverage analysis using rcov"
task :coverage do
  rm_rf "coverage"
  files = Dir[test_files_pattern]
  system "rcov --rails --sort coverage -Ilib #{files.join(' ')}"
end

desc 'Update documentation on website'
task :sync_docs => 'rdoc' do
  `rsync -ave ssh doc/ dev@dev.thoughtbot.com:/home/dev/www/dev.thoughtbot.com/shoulda`
end

desc 'Default: run tests.'
task :default => ['test']

spec = Gem::Specification.new do |s|
  s.name              = "shoulda"
  s.version           = Thoughtbot::Shoulda::VERSION
  s.summary           = "Making tests easy on the fingers and eyes"
  s.homepage          = "http://thoughtbot.com/projects/shoulda"
  s.rubyforge_project = "shoulda"

  s.files       = FileList["[A-Z]*", "{bin,lib,test}/**/*"]
  s.executables = s.files.grep(/^bin/) { |f| File.basename(f) }

  s.has_rdoc         = true
  s.extra_rdoc_files = ["README.rdoc", "CONTRIBUTION_GUIDELINES.rdoc"]
  s.rdoc_options     = ["--line-numbers", "--inline-source", "--main", "README.rdoc"]

  s.authors = ["Tammer Saleh"]
  s.email   = "tsaleh@thoughtbot.com"

  s.add_dependency "activesupport", ">= 2.0"
end

Rake::GemPackageTask.new spec do |pkg|
  pkg.need_tar = true
  pkg.need_zip = true
end

desc "Clean files generated by rake tasks"
task :clobber => [:clobber_rdoc, :clobber_package]

desc "Generate a gemspec file for GitHub"
task :gemspec do
  File.open("#{spec.name}.gemspec", 'w') do |f|
    f.write spec.to_ruby
  end
end
