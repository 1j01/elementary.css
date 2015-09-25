
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

request = require "request"

task "pull-latest", ->
	launchpad_homepage = "https://launchpad.net/egtk"
	request launchpad_homepage, (err, res, body)->
		throw err if err
		if res.statusCode is 200
			m = body.match /<a href="([^"]*)"\s+title="(Source tarball)"/
			if m?
				[_, tarball_url, title] = m
				console.log """
					#{title} URL: #{tarball_url}
					Download it yourself, and extract it to egtk/
					You can use something like this if you can't extract a .tar.xz file: http://archive.online-convert.com/convert-to-zip
					Comment out color: @transparent; in gtk-widgets.css
					Fix missing commas in gtk-widgets.css and gtk-widgets-dark.css (GtkMessageDialog .titlebar:backdrop)
					Then npm run prepublish
					I'm probably the laziest script ever
				"""
			else
				console.log "Tarball link not found on page #{launchpad_homepage}"
		else
			throw new Error "Request for #{launchpad_homepage} returned HTTP status code #{res.statusCode}"
