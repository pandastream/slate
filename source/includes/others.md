# Others

## HLS Encryption

To use the encryption, Panda will require a 128 bit key and a 128 bit initialization vector. If you set up your HLS profile without them, then we will generate them for you during the first encoding.
If you are willing to use your own key or initialization vector (iv), you can create them yourself.

For example, using Ruby :

```ruby
    cipher = OpenSSL::Cipher::AES.new(128, :CBC)
    cipher.encrypt
    key = cipher.random_key
    iv = cipher.random_iv
```
<div style="clear: both;"></div>
In order to use your generated key or initialization vector in the profile settings, you will have to first **base 64** encode them. Make sure to remove any newline characters at the end.
Example, using Ruby :

```ruby
    encoded_key = Base64.encode64(key).chomp
    encoded_iv = Base64.encode64(iv).chomp
```
<div style="clear: both;"></div>
