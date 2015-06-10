
{writeFileSync, mkdirSync} = require "fs"
{join} = require "path"
postgtk = require "postcss-gtk"
autoprefixer = require "autoprefixer-core"
csswring = require "csswring"

task "build", ->
	
	output_dir = "lib"
	try mkdirSync output_dir
	
	build = (to, {from})->
		output_path = join output_dir, to
		source_map_output_path = "#{output_path}.map"
		entry_point_css = """
			@import "./egtk/gtk-3.0/#{from}";
			@import "./fixes.css";
		"""
		postgtk
			.use autoprefixer browsers: ["> 1%"]
			.use csswring
			.process entry_point_css, to: output_path, map: inline: yes
			.then (result)->
				writeFileSync output_path, result.css, "utf8"
				console.log "wrote #{output_path}"
			.catch (e)->
				console.error e.stack
	
	build "elementary.css", from: "gtk.css"
	build "elementary-dark.css", from: "gtk-dark.css"
