# Single-Sign-On (SSO) Setup

## Overview

leanIX can be configured to work with three kinds of IDPs:

* internal IDP (the one you see when log in to app.leanix.net)
* internal LDAP-based IDP (we can provide an IDP based on Shibboleth that runs in a docker container) 
* external IDP

Workspaces for customers that want to use a SSO setup other than the default internal IDP have to be on a dedicated instance that is configured to use a dedicated IDP.

leanIX is tested and works with the following IDPs:
<table>
   <tr>
      <td>Shibboleth IDP</td>
      <td>https://wiki.shibboleth.net/confluence/display/SHIB2/</td>
   </tr>
   <tr>
      <td>Microsoft Active Directory Federation Services (AD FS)</td>
      <td>https://msdn.microsoft.com/en-us/library/bb897402.aspx</td>
   </tr>  
   <tr>
      <td>CA Single Sign On</td>
      <td>http://www.ca.com/de/securecenter/ca-single-sign-on.aspx</td>   
   </tr>
</table>

## Checklist external IDP

Prerequisites:

* IDP must support SAML 2.0
* client browser needs network access to IDP
* LeanIX needs a test-user to login to the client IDP

Decisions:

1. Is the role of a user maintained in leanIX or IDP? (most customers choose IDP here)
  * Externally managed: Roles can be extracted directly from the directory of the customer, e.g. from LDAP or Active Directory groups
  * Managed by leanIX: Roles can be set during invite of a new user and can later be changed in the user administration
2. Is the field `uid` (see Attribute Mapping below) that the IDP is providing unique in a sense that it never collides with any other user in leanIX's user record?
  * Externally managed: The IDP sends uids that already contain a customer prefix like `uid=123456@customer.xyz`.
  * Managed by leanIX: The IDP sends uids like `uid=123456` and leanIX takes care of uniqueness by adding a company prefix. The resulting uid in leanIX's user record would than be `uid=123456@customer.xyz`.  


Approach:

1. Customer provides metadata-idp.xml (exported from their IDP)
2. Customer sets up a test user for LeanIX to ease assistence in the SSO setup process
3. LeanIX sets up a dedicated instance of leanIX and configures it to use the customer's IDP
4. LeanIX provides metadata-sp.xml (can also be downloaded, e.g. https://customer.leanix.net/Shibboleth.sso/Metadata)
5. Customer imports metadata-sp.xml and configures SAML attributes that leanIX SP requires
6. LeanIX + customer both test the authentication in a WebEx

## Attribute Mapping

LeanIX requires that the following attributes are submitted in the SAML 2.0 message.

<table>
<tr>
   <th>Name</th>
   <th>Format</th>
   <th>Example</th>
   <th>Comment</th>
</tr>
<tr>
   <td>firstname</td>
   <td>urn:oasis:names:tc:SAML:2.0:attrname-format:uri</td>
   <td>Peter</td>
   <td></td>
</tr>
<tr>
   <td>lastname</td>
   <td>urn:oasis:names:tc:SAML:2.0:attrname-format:uri</td>
   <td>Schmidt</td>
   <td></td>
</tr>
<tr>
   <td>fullname</td>
   <td>urn:oasis:names:tc:SAML:2.0:attrname-format:uri</td>
   <td>Peter Schmidt</td>
   <td>Optional</td>
</tr>
<tr>
   <td>uid</td>
   <td>urn:oasis:names:tc:SAML:2.0:attrname-format:uri</td>
   <td>55201001@customer.com</td>
   <td>Unique ID of user, stays stable even if Name is changed. Must be in e-mail format</td>
</tr>
<tr>
   <td>mail</td>
   <td>urn:oasis:names:tc:SAML:2.0:attrname-format:uri</td>
   <td>peter.schmidt@customer.com</td>
   <td></td> 
</tr>
<tr>
   <td>role</td>
   <td>urn:oasis:names:tc:SAML:2.0:attrname-format:uri</td>
   <td>MEMBER</td>
   <td>One of ADMIN, MEMBER, VIEWER. In case multiple roles are submitted comma-separated, then the highest role is taken. This attribute can be omitted if the role of a user is mananged by leanIX.</td> 
</tr>
</table>

Please ensure that the name of every SAML 2.0 attribute exactly matches the name leanIX SP expects. For Microsoft AD FS this requires that you have to apply custom rule mappings, see [AD FS Technical Reference](https://technet.microsoft.com/en-us/library/dd807118.aspx).  

### Example of a working SAML message

```
<saml2:AttributeStatement>
   <saml2:Attribute FriendlyName="firstname" Name="firstname" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
      <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">
      Peter
      </saml2:AttributeValue>
   </saml2:Attribute>
   <saml2:Attribute FriendlyName="lastname" Name="lastname" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
      <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">
      Schmidt
      </saml2:AttributeValue>
   </saml2:Attribute>
   <saml2:Attribute FriendlyName="fullname" Name="fullname" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
      <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">
      Peter Schmidt
      </saml2:AttributeValue>
   </saml2:Attribute>
   <saml2:Attribute FriendlyName="uid" Name="uid" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
      <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">
      55201001@customer.com
      </saml2:AttributeValue>
   </saml2:Attribute>
   <saml2:Attribute FriendlyName="mail" Name="urn:oid:0.9.2342.19200300.100.1.3" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
      <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">
      peter.schmidt@customer.com
      </saml2:AttributeValue>
   </saml2:Attribute>
   <saml2:Attribute FriendlyName="role" Name="urn:oid:2.5.6.8" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
      <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">
      MEMBER
      </saml2:AttributeValue>
   </saml2:Attribute>
</saml2:AttributeStatement>

```

## Debugging & Error-Resolution

* SAML logs can be found in the SP `var/log/shibboleth/shibd.log` in eam-web container of the SSO instance.

## Important remarks

* Invitation mode can only be direct - must be checked when workspace is changed
