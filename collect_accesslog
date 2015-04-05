#!/opt/csw/bin/ruby
####################################################################
# NAME: Collect_accesslog.rb
#
# PURPOSE: Collect yesterday's access log file via ftp, and
# save the file to "/log/access_log/support.jp-443.access"
# directory for Webtrends to collect. Also, save a compressed
# copy to "/log/access_log/archive" directory for history.
#
####################################################################
require 'net/ftp'
require 'ftools'
require 'fileutils'

# Move to local directory.
Dir.chdir '/var/tmp/support_archive_tmp/'

# Determine yesterday's date
yesterday = Time.now - (24 *60 *60)
kino = yesterday.strftime("%Y%m%d")

# Open an ftp session.
ftp = Net::FTP.open('10.10.10.4') do |ftp|
ftp.login('co001@219.163.121.9', 'password')
ftp.chdir('/var/www/co/accesslog/')

# Collect yesterday's access log file.
ftp.gettextfile("access_log." + kino) { |line| }
puts 'Downloaded access log.'
end

# Rename, change ownership and permissions.
Dir["**/access_log.*"].each do |folder|
File.rename(folder, folder.sub('access_log.', 'cob2b_'))
File.chown(8005, 1, "cob2b_" + kino)
File.chmod(0600, "cob2b_" + kino)
end

# Add ".log" to end of the file name.
Dir.glob("*").each do |file|
File.new(file, "r").gets
newfile = file + ".log"
File.rename(file, newfile)
end

# Copy files to Webtrend collection directory.
system("/bin/cp /var/tmp/support_archive_tmp/cob2b* /log/access_log/support.jp-443.access")
puts 'Access log file placed in the Webtrend collection directory.'

# Compress file, and copy file to archive.
system("/usr/local/bin/gzip -f /var/tmp/support_archive_tmp/cob2b*")
system("/bin/mv /var/tmp/support_archive_tmp/cob2b* /log/access_log/archive/")
puts 'Compressed access log file placed in archive.'
