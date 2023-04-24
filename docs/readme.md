# kmscsrbuilder Documentation

*kmscsrbuilder* is a Python library for constructing CSRs - certificate signing
requests with KMS Keys forked from [csrbuilder](https://github.com/wbond/csrbuilder). It provides a high-level interface with knowledge of RFC 2986 to
produce, valid, correct requests without terrible APIs or hunting through RFCs.

Since its only dependencies are the
[*asn1crypto*](https://github.com/wbond/asn1crypto#readme) and
[*oscrypto*](https://github.com/wbond/oscrypto#readme) libraries, it is
easy to install and use on Windows, OS X, Linux and the BSDs.

The documentation consists of the following topics:

 - [Basic Usage](#basic-usage)
 - [API Documentation](api.md)

## Basic Usage

A simple CSR can be created by generating a public/private
key pair using *oscrypto* and then passing a dictionary of name information to
the `KMSCSRBuilder()` constructor:

```python
import sys
sys.path.append('.\kmsCsrBuilder')

from kmsCsrBuilder import KMSCSRBuilder, pem_armor_csr

kms_arn = 'arn:aws:kms:eu-west-1:xxxxxxxxxxxx:key/1234abcd-12ab-34cd-56ef-1234567890ab'

builder = KMSCSRBuilder(
    {
        'country_name': 'IE',
        'state_or_province_name': 'Meath',
        'locality_name': 'East Meath',
        'organization_name': 'Palmep Tech',
        'common_name': 'Patrick',
    },
    kms_arn
)
# Add subjectAltName domains
builder.subject_alt_domains = ['palmep.tech']
request = builder.build_with_kms(kms_arn)

with open('.\example-kms.csr', 'wb') as f:
    f.write(pem_armor_csr(request))
```

All name components must be unicode strings. Common name keys include:

 - `country_name`
 - `state_or_province_name`
 - `locality_name`
 - `organization_name`
 - `common_name`

Less common keys include:

 - `organizational_unit_name`
 - `email_address`
 - `street_address`
 - `postal_code`
 - `business_category`
 - `incorporation_locality`
 - `incorporation_state_or_province`
 - `incorporation_country`

See [`CSRBuilder.subject`](api.md#subject-attribute) for a full
list of supported name keys.
