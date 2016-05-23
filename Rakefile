task :default => :new

require 'fileutils'

desc "创建新 post"
task :new do
    puts "请输入要创建的 post URL："
	@url = STDIN.gets.chomp 

	@slug = "#{@url}"
	@slug = @slug.downcase.strip.gsub(' ', '-')

	@date = Time.now.strftime("%F")
	@post_name = "_posts/#{@date}-#{@slug}.md"
	if File.exist?(@post_name)
			abort("文件名已经存在！创建失败")
	end
	FileUtils.touch(@post_name)
	open(@post_name, 'a') do |file|
			file.puts "---"
			file.puts "layout: post"
			file.puts "title: #{@name}"  
			file.puts "date: #{Time.now}"
			file.puts "categories: #{@categories}"
			file.puts "tags: #{@tag}"
			file.puts "---"
			file.puts "* content"
			file.puts "{:toc}"
	end
	exec "vi #{@post_name}"
end