desc "Remove cruft generated by compilation"
task :clean do
  files_to_clean = File.read('.gitignore').split("\n").map { |i| "**/#{i}"}
  p files_to_clean
  Dir.glob(files_to_clean).each do |file|
    rm file
  end
end

task :rm do
  system("rm -Rf tmp")
end

desc "Install all required tools for compilation"
task :setup do
  mkdir_p "tmp"
  Dir.chdir("tmp") do
    # wget
    if File.exist? "/usr/local/bin/wget"
      puts "wget is already installed"
    else
      system("curl -a -O http://ftp.gnu.org/gnu/wget/wget-1.11.4.tar.bz2")
      system("bunzip2 wget-1.11.4.tar.bz2")
      system("tar xf wget-1.11.4.tar")
      Dir.chdir("wget-1.11.4") do
        system("./configure")
        system("make install")
      end
    end

    # pcre
    if File.exist? "/usr/local/bin/pcregrep"
      puts "pcre is already installed"
    else
      system("curl -a -O ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-7.9.tar.bz2")
      system("bunzip2 pcre-7.9.tar.bz2")
      system("tar xf pcre-7.9.tar")
      Dir.chdir("pcre-7.9") do
        system("./configure")
        system("make install")
      end
    end

    # godi
    if File.exist? "/opt/godi/bin/ocaml"
      puts "GODI is already installed"
    else
      system("curl -a -O http://download.camlcity.org/download/godi-rocketboost-20090916.tar.gz")
      system("tar xvzf godi-rocketboost-20090916.tar.gz")
      mkdir_p "godi"
      Dir.chdir("godi-rocketboost-20090916") do
        system("./bootstrap")
        system("PATH=/opt/godi/bin:/opt/godi/sbin:$PATH && ./bootstrap_stage2")
      end
    end
  end
end

def version
  File.read("VERSION").chomp
end

desc "Compile MTASC"
task :compile do
  system("PATH=/opt/godi/bin:/opt/godi/sbin:$PATH && ocaml install.ml")
end

desc "Package MTASC for release"
task :package => :compile do
  system("rm -Rf pkg/#{version}") if File.exist?("pkg/#{version}")
  mkdir_p "pkg/#{version}"
  system("cp -R bin/mtasc* pkg/#{version}/")
  system("cp LICENSE pkg/#{version}/")
  system("cp LICENSE pkg/#{version}/")
  system("cp -R src/mtasc/std* pkg/#{version}/")
end

desc "Install MTASC to /usr/local"
task :install => :compile do
  system("cp bin/mtasc /usr/local/bin/mtasc-#{version}")
  system("rm /usr/local/bin/mtasc")
  system("ln -s /usr/local/bin/mtasc-#{version} /usr/local/bin/mtasc")
  system("cp -R src/mtasc/std* /usr/local/bin/")
end

task :default => :compile