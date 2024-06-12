# kmscsrbuilder

A Python library for creating and signing X.509 certificate signing requests
(CSRs) with KMS Keys.

 - [Related Crypto Libraries](#related-crypto-libraries)
 - [Current Release](#current-release)
 - [Dependencies](#dependencies)
 - [Usage](#usage)
 - [License](#license)
 - [Documentation](#documentation)

## Related Crypto Libraries

*kmscsrbuilder* is a fork of [csrbuilder](https://github.com/wbond/csrbuilder), part of the modularcrypto family of Python packages:

 - [asn1crypto](https://github.com/wbond/asn1crypto)
 - [oscrypto](https://github.com/wbond/oscrypto)
 - [csrbuilder](https://github.com/wbond/csrbuilder)
 - [certbuilder](https://github.com/wbond/certbuilder)
 - [crlbuilder](https://github.com/wbond/crlbuilder)
 - [ocspbuilder](https://github.com/wbond/ocspbuilder)
 - [certvalidator](https://github.com/wbond/certvalidator)

## Current Release

0.0.1 - [changelog](changelog.md)

## Dependencies

 - [*asn1crypto*](https://github.com/wbond/asn1crypto)
 - [*oscrypto*](https://github.com/wbond/oscrypto)
 - Python 2.6, 2.7, 3.2, 3.3, 3.4, 3.5, 3.6, 3.7, 3.8, 3.9 or pypy

## Usage

```bash
git clone git@ssh.gitlab.aws.dev:palmep/kms-csr_builder.git kmsCsrBuilder
pip3 install asn1crypto oscrypto
```

```python
import sys
sys.path.append('.\kmsCsrBuilder')

from kmsCsrBuilder import KMSCSRBuilder, pem_armor_csr

kms_arn = 'arn:aws:kms:eu-west-1:111122223333:key/a1b2c3d4-5678-90ab-cdef-EXAMPLE11111'

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
builder.subject_alt_domains = ['xks.palmep.tech', 'www.palmep.tech']
request = builder.build_with_kms(kms_arn)

with open('.\example-kms.csr', 'wb') as f:
    f.write(pem_armor_csr(request))
```

## License

*kmscsrbuilder* is licensed under the terms of the MIT license. See the
[LICENSE](LICENSE) file for the exact license text.

## Documentation

[*kmscsrbuilder* documentation](docs/readme.md)


