#!/usr/bin/env ruby

require 'fileutils'
require 'digest'

path = File.expand_path(File.dirname(__FILE__))

def apply_vars(text, vars)
  vars.each do |name, val|
    text = text.gsub(name, val.inspect)
  end
  text
end

def bump_version(version)
  s = version.split(".")
  s[-1] = (s[-1].to_i + 1).to_s
  s.join(".").strip
end


Dir.chdir(path) do


  css = File.read("discourse.css")

  bundle = File.read("templates/custom_css_bundle.js")

  plugin = File.read("templates/plugin.json")

  version = bump_version(File.read("version"))
  File.write("version", version)

  id = "custom-css-#{version.split(".").join}"

  bundle = apply_vars(bundle, "$id$" => id, "$CSS$" => css)
  bundle_name = "custom_css_#{Digest::SHA1.hexdigest(css)}.js"
  plugin = apply_vars(plugin, "$id$" => id, "$VERSION$" => version, "$BUNDLE_PATH$" => bundle_name)

  FileUtils.mkdir_p("dist")

  Dir.chdir("dist") do
    `rm -f *.gz`
    File.write(bundle_name, bundle)
    File.write("plugin.json", plugin)
    `tar -czvf custom_css.tar.gz .`
    `rm plugin.json`
    `rm *.js`
  end

end

