# Rquire jekyll to compile the site.
require "jekyll"
require "pry"

# Github pages publishing.
namespace :blog do
  #
  # Because we are using 3rd party plugins for jekyll to manage the asset pipeline
  # and suchlike we are unable to just branch the code, we have to process the site
  # localy before pushing it to the branch to publish.
  #
  # We built this little rake task to help make that a little bit eaiser.
  #

  # Usaage:
  # bundle exec rake blog:publish
  desc "Publish blog to gh-pages"
  task :publish do
    # Compile the Jekyll site using the config.
    Jekyll::Site.new(Jekyll.configuration({
      "source"      => ".",
      "destination" => "_site",
      "config" => "_config.yml"
    })).process

    # Get the origin to which we are going to push the site.
    origin = `git config --get remote.origin.url`

    # Make a temporary directory for the build before production release.
    # This will be torn down once the task is complete.
    Dir.mktmpdir do |tmp|
      # Copy accross our compiled _site directory.
      cp_r "_site/.", tmp

      # Switch in to the tmp dir.
      Dir.chdir tmp

      # Prepare all the content in the repo for deployment.
      system "git init" # Init the repo.
      system "git add . && git commit -m 'Site updated at #{Time.now.utc}'" # Add and commit all the files.

      # Add the origin remote for the parent repo to the tmp folder.
      system "git remote add origin #{origin}"

      # Push the files to the gh-pages branch, forcing an overwrite.
      system "git push origin master --force"
    end

    # Done.
  end

  def resize name, w, h
    sh %{mogrify -resize #{MAX_GEOMETRY[:w]}x#{MAX_GEOMETRY[:h]} '#{name}'}
  end

  MAX_GEOMETRY = { w: 1280.0, h: 960.0 }
  namespace :jpeg do
    task :list do
      @jpgs = Dir.glob("**/*.jpg") - Dir.glob("_site/**/*.jpg")
    end

    desc "Scale down images to max geometry allowed"
    task :resize => :list do
      files= { }

      @jpgs.each do |jpg|
        IO.popen "identify '#{jpg}'" do |p|
          f = p.read
          name,bla,size = f.split(/ /)
          name.sub!(/jpg\[\d+\]$/, 'jpg')
          w,h = size.split('x').map(&:to_i)
          next unless name && w && h
          if w > MAX_GEOMETRY[:w] || h > MAX_GEOMETRY[:h]
            previous_size = File.size?(name)

            resize(name, w, h)

            size = File.size?(name)
            reduction = ((previous_size-size)*100.0/previous_size)
            puts "size reduction of %d; resizing #{name}" % reduction
          end
        end
      end
    end

    desc "Compress JPEG images"
    task :minimize => :list do
      @jpgs.each do |f|
        sh "jpegoptim --strip-all --totals -o '#{f}'"
      end
    end

    desc "Do all the image processing"
    task :compress => [:resize, :minimize, :list] do
      puts "Finished image processing"
    end
  end
end
