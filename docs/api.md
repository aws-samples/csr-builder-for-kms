# kmscsrbuilder API Documentation

### `pem_armor_csr()` function

> ```python
> def pem_armor_csr(certification_request):
>     """
>     :param certification_request:
>         An asn1crypto.csr.CertificationRequest object of the CSR to armor.
>         Typically this is obtained from CSRBuilder.build().
>
>     :return:
>         A byte string of the PEM-encoded CSR
>     """
> ```
>
> Encodes a CSR into PEM format

### `KMSCSRBuilder()` class

> ##### constructor
>
> > ```python
> > def __init__(self, subject, kms_arn):
> >     """
> >     :param subject:
> >         An asn1crypto.x509.Name object, or a dict - see the docstring
> >         for .subject for a list of valid options
> >
> >     :param kms_arn:
> >         KMS Key Pair ARN with key usage SIGN_VERIFY
> >     """
> > ```
> >
> > Unless changed, CSRs will use SHA-256 for the signature
>
> ##### `.subject` attribute
>
> > An asn1crypto.x509.Name object, or a dict with at least the
> > following keys:
> >
> >  - country_name
> >  - state_or_province_name
> >  - locality_name
> >  - organization_name
> >  - common_name
> >
> > Less common keys include:
> >
> >  - organizational_unit_name
> >  - email_address
> >  - street_address
> >  - postal_code
> >  - business_category
> >  - incorporation_locality
> >  - incorporation_state_or_province
> >  - incorporation_country
> >
> > Uncommon keys include:
> >
> >  - surname
> >  - title
> >  - serial_number
> >  - name
> >  - given_name
> >  - initials
> >  - generation_qualifier
> >  - dn_qualifier
> >  - pseudonym
> >  - domain_component
> >
> > All values should be unicode strings
>
> ##### `.subject_public_key` attribute
>
> > An oscrypto.asymmetric.PublicKey object of the KMS Key public key.
>
> ##### `.kms_arn` attribute
>
> > The string of the KMS Key ARN
>
> ##### `.hash_algo` attribute
>
> > A unicode string of the hash algorithm to use when signing the
> > request - "sha1" (not recommended), "sha256" (default) or "sha512"
>
> ##### `.kms_signature_algo` attribute
>
> > A string of the signing algorithm to use when signing the
> > request using KMS - valid values available https://docs.aws.amazon.com/kms/latest/APIReference/API_Sign.html
> > Does not accept SM2DSA
>
> > ```python
> > builder = KMSCSRBuilder(
> >    {
> >        'country_name': 'IE',
> >        'state_or_province_name': 'Meath',
> >        'locality_name': 'East Meath',
> >        'organization_name': 'Amazon Web Services',
> >        'common_name': 'Patrick',
> >    },
> >    kms_arn
> > )
> > builder.kms_signature_algo = 'ECDSA_SHA_256'
> > request - builder.build_with_kms(kms_arn)
> > ```
>
> ##### `.ca` attribute
>
> > None or a bool - if the request is for a CA cert. None indicates no
> > basic constraints extension request.
>
> ##### `.subject_alt_domains` attribute
>
> > A list of unicode strings of all domains in the subject alt name
> > extension request. Empty list indicates no subject alt name extension
> > request.
>
> ##### `.subject_alt_ips` attribute
>
> > A list of unicode strings of all IPs in the subject alt name extension
> > request. Empty list indicates no subject alt name extension request.
>
> ##### `.key_usage` attribute
>
> > A set of unicode strings representing the allowed usage of the key.
> > Empty set indicates no key usage extension request.
> > Must be a value of the set ["digital_signature", "non_repudiation", "key_encipherment", 
> > "data_encipherment", "data_encipherment", "key_cert_sign", 
> > "key_cert_sign", "key_cert_sign", "decipher_only"]
> > See KeyUsage class in [asn1crypto.x509](https://github.com/wbond/asn1crypto/blob/master/asn1crypto/x509.py)
>
> > ```python
> > builder = KMSCSRBuilder(
> >    {
> >        'country_name': 'IE',
> >        ...
> >        'organization_name': 'Amazon Web Services',
> >        'common_name': 'Patrick',
> >    },
> >    kms_arn
> > )
> > builder.key_usage = set(['digital_signature'])
> > request = builder.build_with_kms(kms_arn)
> > ```
>
>
> ##### `.extended_key_usage` attribute
>
> > A set of unicode strings representing the allowed usage of the key from
> > the extended key usage extension. Empty set indicates no extended key
> > usage extension request.
> > Valid strings can be found in [asn1crypto.x509.KeyPurposeId](https://github.com/wbond/asn1crypto/blob/master/asn1crypto/x509.py#L1682)
>
> > ```python
> > builder = KMSCSRBuilder(
> >    {
> >        'country_name': 'IE',
> >        ...
> >        'organization_name': 'Amazon Web Services',
> >        'common_name': 'Patrick',
> >    },
> >    kms_arn
> > )
> > builder.extended_key_usage = set(['code_signing'])
> > request = builder.build_with_kms(kms_arn)
> > ```
>
>
> ##### `.set_extension()` method
>
> > ```python
> > def set_extension(self, name, value):
> >     """
> >     :param name:
> >         A unicode string of an extension id name from
> >         asn1crypto.x509.ExtensionId
> >
> >     :param value:
> >         A value object per the specs defined by asn1crypto.x509.Extension
> >     """
> > ```
> >
> > Sets the value for an extension using a fully constructed Asn1Value
> > object from asn1crypto. Normally this should not be needed, and the
> > convenience attributes should be sufficient.
> >
> > See the definition of asn1crypto.x509.Extension to determine the
> > appropriate object type for a given extension. Extensions are marked
> > as critical when RFC5280 or RFC6960 indicate so. If an extension is
> > validly marked as critical or not (such as certificate policies and
> > extended key usage), this class will mark it as non-critical.
>
> ##### `.build_with_kms()` method
>
> > ```python
> > def build_with_kms(self, kms_arn):
> >     """
> >     :param kms_arn:
> >         An arn for the KMS Key to sign the request with. This should be
> >         the ARN that matches the public key.
> >
> >     :return:
> >         An asn1crypto.csr.CertificationRequest object of the request
> >     """
> > ```
> >
> > Validates the certificate information, constructs an X.509 certificate
> > and then signs it
