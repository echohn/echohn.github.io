require 'time'

desc "Auto Check on my jekyll server."
task :autocheck do
  exec "god start -c .autocheck.god"
end

desc "Check Github update and build on my jekyll server."
task :check_and_build do
  Dir.chdir File.expand_path('..',__FILE__)
  remote_head = `git ls-remote origin`.lines.map(&:split).first[0]
  locale_head = `git show-ref`.lines.map(&:split).first[0]

  unless remote_head == locale_head
    system 'git checkout .'
    system 'git pull origin master > /dev/null'
    system "jekyll build"
  end
end


desc "Build my site ..."
task :build do
  system "jekyll build"
end

desc "Start a locally server"
task :s do
  system "jekyll server -s . -d _site -P 3000 -w"
end

# Usage: rake post title="A Title" [date="2014-04-14"]
desc "Create a new post"
task :new do
  unless FileTest.directory?('./_posts')
    abort("rake aborted: '_posts' directory not found.")
  end

  title = ENV["title"] || "new-post"
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  begin
    date = (ENV['date'] ? Time.parse(ENV['date']) : Time.now)
           .strftime('%Y-%m-%d')
  rescue Exception => e
    puts "Error: date format must be YYYY-MM-DD!"
    exit -1
  end

  filename = File.join('.', '_posts', "#{date}-#{slug}.md")
  if File.exist?(filename)
    abort("rake aborted: #{filename} already exists.")
  end

  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/-/,' ')}\""
    post.puts "date: #{date}"
    post.puts "categories:"
    post.puts "tags: "
    post.puts "---"
  end

  exec "open #{filename}"
end
