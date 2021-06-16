require 'cgi'

task :post, [:title] do |t, args|
  title = args[:title]
  raise "Usage `rake post[title]`" unless title

  date = Time.now.strftime('%Y-%m-%d')
  filename = "_posts/#{date}-#{CGI.escape(title.gsub(" ", "-"))}.markdown"

  if File.exist?(filename)
    abort("#{filename} already exists!")
  end

  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "---"
  end
end
