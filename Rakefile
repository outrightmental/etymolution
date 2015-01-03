require 'rubygems'
require 'rake'
require 'yaml'
require 'time'

SOURCE = '.'
CONFIG = {
    :version => '0.3.0',
    :layouts => File.join(SOURCE, '_layouts'),
    :posts => File.join(SOURCE, '_posts'),
    :post_ext => 'md'
}

# Path configuration helper
module JB
  class Path
    SOURCE = '.'
    Paths = {
        :layouts => '_layouts',
        :posts => '_posts'
    }
    def self.base
      SOURCE
    end
    # build a path relative to configured path settings.
    def self.build(path, opts = {})
      opts[:root] ||= SOURCE
      path = "#{opts[:root]}/#{Paths[path.to_sym]}/#{opts[:node]}".split('/')
      path.compact!
      File.__send__ :join, path
    end
  end #Path
end #JB

# Usage: rake post title="A Title" [date="2012-02-09"] [tags=[tag1,tag2]] [category="category"]
desc "Begin a new post in #{CONFIG['posts']}"
task :post do
  abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
  title = ENV['title'] || 'new-post'
  tags = ENV['tags'] || '[]'
  category = ENV['category'] || ''
  category = "\"#{category.gsub(/-/, ' ')}\"" unless category.empty?
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  begin
    date = (ENV['date'] ? Time.parse(ENV['date']) : Time.now).strftime('%Y-%m-%d')
  rescue => e
    puts "Error #{e.to_s} - date format must be YYYY-MM-DD, please check you typed it correctly!"
    exit -1
  end
  filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_ext']}")
  if File.exist?(filename)
    abort('rake aborted!') if ask("#{filename} already exists. Do you want to overwrite?", %w(y n)) == 'n'
  end
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts '---'
    post.puts 'layout: post'
    post.puts "title: \"#{title.gsub(/-/, ' ')}\""
    post.puts 'description: ""'
    post.puts "category: #{category}"
    post.puts "tags: #{tags}"
    post.puts '---'
    post.puts '{% include JB/setup %}'
  end
end # task :post

# Usage: rake page name="about.html"
# You can also specify a sub-directory path.
# If you don't specify a file extention we create an index.html at the path specified
desc 'Create a new page.'
task :page do
  name = ENV['name'] || 'new-page.md'
  filename = File.join(SOURCE, "#{name}")
  filename = File.join(filename, 'index.html') if File.extname(filename) == ''
  title = File.basename(filename, File.extname(filename)).gsub(/[\W_]/, ' ').gsub(/\b\w/) { $&.upcase }
  if File.exist?(filename)
    abort('rake aborted!') if ask("#{filename} already exists. Do you want to overwrite?", %w(y n)) == 'n'
  end
  mkdir_p File.dirname(filename)
  puts "Creating new page: #{filename}"
  open(filename, 'w') do |post|
    post.puts '---'
    post.puts 'layout: page'
    post.puts "title: \"#{title}\""
    post.puts 'description: ""'
    post.puts '---'
    post.puts '{% include JB/setup %}'
  end
end # task :page

# Launch preview environment
desc 'Launch preview environment'
task :preview do
  system 'jekyll serve -w'
end # task :preview

# Ask the user for input to continue
def ask(message, valid_options)
  valid_options ? (get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /, '/')} ") until valid_options.include?(answer)) : get_stdin(message)
end

# Get user input
def get_stdin(message)
  print message
  STDIN.gets.chomp
end
