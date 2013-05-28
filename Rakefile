# Build information
SOLUTION_NAME = "RestResourceSerializer"
SOLUTION_DESC = "A .NET RESTful service resource serializer, based on the JSON API standard (http://jsonapi.org/)"
SOLUTION_LICENSE = "http://www.opensource.org/licenses/mit-license.php"
SOLUTION_URL = "http://github.com/joeyb/RestResourceSerializer"
SOLUTION_COMPANY = "Joey Bratton"
SOLUTION_COPYRIGHT = "Copyright (C) Joey Bratton 2013"

# Build configuration
load "VERSION"

CONFIG = ENV["config"] || "Release"
PLATFORM = ENV["platform"] || "x86"
BUILD_NUMBER = "#{BUILD_VERSION}.#{(ENV["BUILD_NUMBER"] || Time.new.strftime('5%H%M'))}"
TEAMCITY = (!ENV["BUILD_NUMBER"].nil? or !ENV["TEAMCITY_BUILD_PROPERTIES_FILE"].nil?)
TEAMCITY_BRANCH = !TEAMCITY ? nil : ENV["BRANCH"]

# Paths
BASE_PATH = File.expand_path(File.dirname(__FILE__))
SOURCE_PATH = "#{BASE_PATH}/src"
BUILD_PATH = "#{BASE_PATH}/build"

# Files
ASSEMBLY_INFO = "#{SOURCE_PATH}/CommonAssemblyInfo.cs"
SOLUTION_FILE = "#{SOURCE_PATH}/RestResourceSerializer.sln"

# Setup our the build environment
require 'albacore'
require 'pathname'
require 'rake/clean'

# Configure albacore
Albacore.configure do |config|
    config.log_level = (TEAMCITY ? :verbose : :normal)

    config.msbuild.solution = SOLUTION_FILE
    config.msbuild.properties = { :configuration => CONFIG }
    config.msbuild.use :net4
    config.msbuild.targets = [ :Clean, :Build ]
    config.msbuild.verbosity = "normal"

    CLEAN.include(FileList["#{SOURCE_PATH}/**/obj"])
    CLOBBER.include(FileList["#{SOURCE_PATH}/**/bin"])
    CLOBBER.include(BUILD_PATH)
end

desc "Build"
msbuild :build => [:init, :assemblyinfo]

# Hidden tasks
task :init => [:clobber] do
    if not TEAMCITY
        indexupdate = `git update-index --assume-unchanged #{SOURCE_PATH}/CommonAssemblyInfo.cs`
        raise "Unable to perform git index operation, cannot continue (#{indexupdate})." unless indexupdate.empty?
    end

    Dir.mkdir BUILD_PATH unless File.exists?(BUILD_PATH)
end

assemblyinfo :assemblyinfo do |asm|
    asm_version = BUILD_NUMBER

    begin
        commit = `git log -1 --pretty=format:%H`
    rescue
        commit = "git unavailable"
    end

    asm.language = "C#"
    asm.version = BUILD_NUMBER
    asm.trademark = commit
    asm.file_version = BUILD_NUMBER
    asm.company_name = SOLUTION_COMPANY
    asm.product_name = SOLUTION_NAME
    asm.copyright = SOLUTION_COPYRIGHT
    asm.custom_attributes :AssemblyConfiguration => CONFIG, :AssemblyInformationalVersion => asm_version
    asm.output_file = ASSEMBLY_INFO
    asm.com_visible = false
end