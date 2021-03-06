---

copyright:
  years: 2018
lastupdated: "2018-08-09"

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:python: .ph data-hd-programlang='python'}
{:table: .aria-labeledby="caption"}


# Importing an encrypted image by using the SoftLayer API 

You can use the {{site.data.keyword.slapi_short}} to import an encrypted image from {{site.data.keyword.cos_full}} 
and create an image template. When your image template is created, you can use it to provision instances. 
{:shortdesc}

To limit access to only the information that is needed to complete the import task, authenticate with a service ID. The service ID should have access only to the encrypted image in IBM Cloud Object Storage that you want to import and the Key Protect instance where your root key is stored.  

The following python snippet shows an example of how you can access the 
[SoftLayer_Virtual_Guest_Block_Device_Template_Group ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://softlayer.github.io/reference/services/SoftLayer_Virtual_Guest_Block_Device_Template_Group/) API and use the 
_createFromIcos_ method to create an image template.

```python
import SoftLayer

client = SoftLayer.create_client_from_env(username='<user>',
                        api_key='<api_key>',
                        endpoint_url='https://api.softlayer.com/rest/v3',
                        timeout=240)
group_svc = client['Virtual_Guest_Block_Device_Template_Group']
config = {'name':'my_encrypted_image',
      'note':'my note',
      'operatingSystemReferenceCode':'REDHAT_7_64',
      'uri':'cos://<region_name>/<bucket_name>/xx123.Rhel_7_encrypted.raw',
      'bootMode':'my boot mode',
      'cloud-init': True,
      'byol': True,
      'encrypted': True,
      'ibmApiKey':'<api_key>',
      'rootKeyId':'my root key ID',
      'wrappedDek':'my wrapped DEK',
      'keyProtectId':'<key_protect_instance_id>',
      }
ret = group_svc.createFromIcos(config)
print(ret)
```
{: codeblock}


For more information about locating values that are needed to import the encrypted image from {{site.data.keyword.cos_full_notm}}, see the following table.

| Field    | Value   |
| -------- | ------- |
| ibmApiKey | Specify the API key that you noted when you created it. If the API key is lost, you must create a new API key. For more information, see [Managing your API keys](/docs/iam/userid_keys.html). |
| rootKeyId | Specify the ID of the root key that was used to wrap the data encryption key. For more information, see [Viewing keys](/docs/services/keymgmt/keyprotect_view_keys.html). |
| wrappedDek | Specify the cipher text that is associated with your wrapped data encryption key that you used to encrypt your image. For more information, see [Wrapping keys by using the API](/docs/services/keymgmt/keyprotect_wrap_keys.html). |
| keyProtectId | You can use the {{site.data.keyword.cloud_notm}} CLI to find your {{site.data.keyword.keymanagementserviceshort}} instance ID. For more information, see [Retrieving your instance ID](/docs/services/keymgmt/keyprotect_authentication.html#retrieve_instance_ID). |
{: caption="Table 1. Values needed for importing encrypted image" caption-side="top"}
