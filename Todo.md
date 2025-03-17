# Todo

  * webapp path : https://www.xwiki.org/xwiki/bin/view/Documentation/AdminGuide/Installation/InstallationViaAPT/#HXWikiasrootwebapp28shortURLs29
  * sql indexes : https://www.xwiki.org/xwiki/bin/view/Documentation/AdminGuide/Installation/InstallationWAR/InstallationMySQL/#HIndexes
  * user tuning : https://www.xwiki.org/xwiki/bin/view/Documentation/AdminGuide/Performances/#HLargenumberofusers
  * mod_proxy_ajp : https://www.xwiki.org/xwiki/bin/view/Documentation/AdminGuide/Performances/#HModProxyAJPConfiguration
  * mod_deflate : cfg /etc/apache2/mods-available/deflate.conf https://www.xwiki.org/xwiki/bin/view/Documentation/AdminGuide/Performances/#HModDeflateConfiguration (+ mod_expire)
  * solr : https://www.xwiki.org/xwiki/bin/view/Documentation/AdminGuide/Installation/InstallationViaAPT/#HStandaloneSolrsetup
  * https://www.myxwiki.org/xwiki/bin/view/Infra/

## automate initial cfg

xwiki.properties:
```
xwiki.superadminpassword=mysecretpassword

# Skip interactive setup
xwiki.setupdone=1
```

### install extensions
```yaml
  - name: Install required extensions
    shell: |
        curl -X POST \
          -u Admin:{{ xwiki_admin_password }} \
          -H "Content-Type: application/json" \
          -d '{"id":"org.xwiki.contrib.ldap:ldap-authenticator","version":"latest"}' \
          {{ xwiki_url }}/rest/extension
    args:
      creates: /var/lib/xwiki/data/extension/repository/org%2Exwiki%2Econtrib%2Eldap%3Aldap-authenticator
```
or see https://www.xwiki.org/xwiki/bin/view/Documentation/UserGuide/Features/XWikiRESTfulAPI#HJobresources (Example of Extension Manager installJob)
https://xwi02ber0/xwiki/bin/admin/XWiki/XWikiPreferences?editor=globaladmin&section=XWiki.ExtensionHistory&extensionId=org.xwiki.contrib.ldap%3Aldap-authenticator&extensionVersion=9.15.7&extensionNamespace=wiki%3Axwiki

so we can:
a) enable superadmin
b) create new default admin
c) install extensions etc.
d) disable superadmin
