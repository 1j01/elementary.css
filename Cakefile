
fs = require "fs"
{join} = require "path"
# {extractTarballDownload} = require "tarball-extract"
request = require "request"
# targz = require "tar.gz"
decompress = require "decompress"
decompressTarxz = require "decompress-tarxz"
postgtk = require "postcss-gtk"
autoprefixer = require "autoprefixer-core"
csswring = require "csswring"

task "build", ->
	
	output_dir = "lib"
	try fs.mkdirSync output_dir
	
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
				fs.writeFileSync output_path, result.css, "utf8"
				console.log "wrote #{output_path}"
			.catch (e)->
				console.error e.stack
	
	build "elementary.css", from: "gtk.css"
	build "elementary-dark.css", from: "gtk-dark.css"

task "pull-latest", ->
	launchpad_homepage = "https://launchpad.net/egtk"
	request launchpad_homepage, (err, res, body)->
		throw err if err
		if res.statusCode is 200
			m = body.match /<a href="([^"]*)"\s+title="(Source tarball)"/
			if m?
				[_, tarball_url, title] = m
				console.log "Source tarball URL: #{tarball_url}"
				# temp_file = "temp/egtk.tar.gz"
				# # destination_fiel = ""
				# # extractTarballDownload tarball_url, temp_file, destination_fiel, {}, (err, info)->
				# # 	throw info.error if info?.error
				# # 	throw err if err
				# read = request.get(tarball_url)
				# write = targz().createWriteStream("./egtk")
				# # read.on "close", ->
				# # read.on "end", ->
				# # write.on "finish", ->
				# 	# console.log """
				# 	# 	Downloaded and extracted to ./egtk/
				# 	# 	Comment out color: @transparent; in gtk-widgets.css
				# 	# 	Fix missing commas in gtk-widgets.css and gtk-widgets-dark.css (GtkMessageDialog .titlebar:backdrop)
				# 	# 	Then npm run prepublish
				# 	# 	I'm probably the laziest script ever
				# 	# """
				# write.on "error", (err)->
				# 	console.log "Failed"
				# 	console.error err
				# read.pipe(write)
				# console.log """
				# 	Downloading and extracting to ./egtk/
				# 	You need to comment out color: @transparent; in gtk-widgets.css
				# 	Then npm run prepublish
				# """
				
				# ^^^ wrong file format ^^^
				
				temp_folder = "temp"
				try fs.mkdirSync temp_folder
				temp_file = "#{temp_folder}/egtk.tar.gz"
				request
					.get(tarball_url)
					.on "error", (err)->
						console.log "Failed to get tarball"
						console.error err
					.on "end", ->
						console.log "Downloaded #{temp_file}"
						decompress temp_file, 'egtk',
							plugins: [
								decompressTarxz()
							]
							strip: 1
						.catch (err)->
							console.log "Failed to extract tarball"
							console.error err
						.then ->
							# console.log "Extracted to elementary/"
							# ...
							# console.log "Renamed elementary/ to egtk/"
							# ...can use `strip: 1` instead
							# console.log """
							# 	Extracted to egtk/
							# 	You need to comment out color: @transparent; in gtk-widgets.css
							# 	Then npm run prepublish
							# """
							console.log "Extracted to egtk/"
							monkeypatchee = "egtk/gtk-3.0/gtk-widgets.css"
							console.log "Now npm run prepublish"
							css = fs.readFileSync monkeypatchee, "utf8"
							css = css.replace /(color: @transparent;)/, "/*$1*/"
							fs.writeFileSync monkeypatchee, css, "utf8"
							# color: @transparent;
					.pipe(fs.createWriteStream(temp_file))
			else
				console.log "Source tarball link not found on page #{launchpad_homepage}"
		else
			throw new Error "Request for #{launchpad_homepage} returned HTTP status code #{res.statusCode}"
