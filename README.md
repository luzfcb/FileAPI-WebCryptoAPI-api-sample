# FileAPI-WebCryptoAPI-api-sample

# FileAPI + WebCryptoAPI

This is a simple example of using FILEAPI and WebCryptoAPI to generate SHA-256 hash and get some more information of selected files via input type="file"


please do not use it. This only was used to my learning

## Live sample:

https://luzfcb.github.io/FileAPI-WebCryptoAPI-api-sample/

## Known Issues:
WebCryptoAPI only work on Google Chrome if connection is secure (https)



## References:


http://www.w3.org/TR/file-upload/

http://www.w3.org/TR/WebCryptoAPI/ 

http://www.html5rocks.com/en/tutorials/file/dndfiles 

https://github.com/diafygi/webcrypto-examples 

https://developer.mozilla.org/en-US/docs/Using_files_from_web_applications 

http://caniuse.com/#feat=cryptography 

http://caniuse.com/#search=FileAPI



# CODE:
```html
<p>
    <label for="id_file">File:</label>
    <input id="id_file" name="file_input" type="file" multiple>
</p>

<p>
    <label for="id_textarea">checksum:</label>
    <textarea id="id_textarea" rows="5" cols="140"></textarea>
</p>


<script>
    // https://chromium.googlesource.com/chromium/blink/+/master/LayoutTests/crypto/subtle/resources/common.js#20
    // Builds a hex string representation for an array-like input.
    // "bytes" can be an Array of bytes, an ArrayBuffer, or any TypedArray.
    // The output looks like this:
    //    ab034c99
    function bytesToHexString(bytes) {
        if (!bytes)
            return null;
        bytes = new Uint8Array(bytes);
        var hexBytes = [];
        for (var i = 0; i < bytes.length; ++i) {
            var byteString = bytes[i].toString(16);
            if (byteString.length < 2)
                byteString = "0" + byteString;
            hexBytes.push(byteString);
        }
        return hexBytes.join("");
    }

    function handleFileSelect(evt) {
        var files = evt.target.files; // FileList object

        // Loop through the FileList and generate shasum
        for (var i = 0, f; f = files[i]; i++) {

            // Only process image files.
            //if (!f.type.match('image.*')) {
            //    continue;
            //}

            var reader = new FileReader();
            // Closure to capture the file information.
            reader.onload = (function (theFile) {
                return function (e) {

                    window.crypto.subtle.digest(
                            {
                                name: "SHA-256"
                            },
                            e.target.result
                    )
                            .then(function (hash) {
                                file_informations = JSON.stringify({
                                    'name': theFile.name,
                                    'lastModified': theFile.lastModified,
                                    'lastModifiedDate': theFile.lastModifiedDate,
                                    'size': theFile.size,
                                    'file_type': theFile.type,
                                    'hash_type': 'SHA-256',
                                    'hash_value': bytesToHexString(hash)
                                });

                                //document.getElementById('id_textarea').value += theFile.name + ':SHA-256:' + bytesToHexString(hash) + '\n';
                                document.getElementById('id_textarea').value += file_informations + ',\n';

                            })
                            .catch(function (err) {
                                console.error(err);
                            });
                };
            })(f);

            // Read in the image file as a data URL.
            reader.readAsArrayBuffer(f);
        }
    }
    document.getElementById('id_file').addEventListener('change', handleFileSelect, false);
</script>

```
