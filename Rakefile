require 'time'

def convert_block_area(file_name)
  flag_in_block = false
  lines = IO.readlines(file_name).map do |l|
    if l.chomp.match /(\s*)```(.*)$/
      if flag_in_block
        flag_in_block = false
        "{% endhighlight %}\n"
      else
        if $2.length > 0
          flag_in_block = true
          "#{$1}{% highlight #{$2} %}\n"
        else
          l
        end
      end
    else
      l
    end
  end

  File.open(file_name,'w') do |f|
    f.puts lines
  end
end

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
  Dir.chdir File.expand_path('..',__FILE__)
  Dir['_posts/*.md'].each do |md_file|
    convert_block_area md_file
  end
  system "jekyll build"
  system "cd ../echohn.github.io && git add ./ && git commit -m 'auto commit' && git push origin master"
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
    post.puts "subtitle: \'\'"
    post.puts "date: #{date}"
    post.puts "header-img: \"\""
    post.puts "author: \"Echo\""
    post.puts "tags: "
    post.puts "keywords: "
    post.puts "---"
  end

  exec "open #{filename}"
end
