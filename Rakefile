#!/usr/bin/env rake
require 'bundler/gem_tasks'

require 'rake'
require 'rspec/core/rake_task'

desc 'Run all examples'
RSpec::Core::RakeTask.new(:spec) do |t|
  t.rspec_opts = %w(--color)
end

task default: [:spec]

desc 'Test and Clean YAML files'
task :clean_yaml do
  require 'yaml'

  d = Dir['**/*.yaml']
  d.each do |file|
    begin
      puts "checking : #{file}"
      data = YAML.load_file(file)
      File.open(file, 'w') { |f| f.write data.to_yaml }
    rescue
      puts "failed to read #{file}: #{$ERROR_INFO}"
    end
  end
end

desc 'Cache Translations'
task :cache_translations do
  require 'yaml'
  require 'i18n_data'
  Codes = YAML.load_file(File.join(File.dirname(__FILE__), 'lib', 'data', 'countries.yaml')) || {}
  Data = {}
  empty_translations_hash = {}
  I18nData.languages.each { |l, _n| empty_translations_hash[l.downcase] = nil }

  I18nData.languages.keys.each do |locale|

    begin
      local_names = I18nData.countries(locale)
    rescue I18nData::NoTranslationAvailable
      next
    end
    
    Codes.each do |alpha2|
      Data[alpha2] ||= {}
      Data[alpha2]['translations'] ||= empty_translations_hash.dup
      Data[alpha2]['translations'][locale.downcase] = local_names[alpha2]
      Data[alpha2]['translated_names'] ||= []
      Data[alpha2]['translated_names'] << local_names[alpha2]
    end
  end

  File.open(File.join(File.dirname(__FILE__), 'lib', 'cache', 'translations.yaml'), 'w+') { |f| f.write Data.to_yaml   }
end
