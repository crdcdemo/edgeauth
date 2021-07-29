To protect the high-value live streaming content from hot links, KanVideo defines its own Edge Token Authentication Signature to verify the request is coming from a valid web player or app.

To achieve more flexibility and extensibility, the Edge Token Authentication Signature was build dynamically. The plain text string which is used for signature calculations is made up of a variable number of key/value parameters in a variable order, and the selection of the key in the parameters may change from time to time too.

Considering the Akamai PM configuration file was actually a XML file, which lacks data structures such as array, List, Object, and cannot implement a foreach loop either. So it is too difficult to implement the Authentication Signature with the Akamai PM only. 

I’m very happy to learn that Akamai provides a new serverless computing solution, EdgeWorkers which can support javascripts programming. Thus I will use EdgeWorkers to build my Edge Token Authentication Signature.

This is the Edge Token Authentication Signature procedure:

1. Get the value of query string key sigparames, if sigparames is not present, then response 403 Access Denied.
2. The value of sigparames is a comma-separated list of keys.
3. Get each of the value of the keys in step 2 from the full query string, and present them with  the form of ‘key=value , then concat them with & in’, and then add the key/value of ‘sigparams=value’ in the end. Then we name this sting as key_value_pairs. Attention, the key/value pairs’ order matters, must be identical with the origin key order in step2.
4. expires is a mandatory key in the query string, it must present and the value must larger than then current Unix Epoch time. if not, we response 403 access denied.
5. len is a mandatory key in the query string, it must present and the value must no less than 0.  if not, we response 403 access denied.
6. Build the plain text for the calculation,  plainText= PATH + Secret + key_value_pairs. PATH is the request path, Secret is a shared secret between the CDN and the web player/app, key_value_pairs is defined in step 3.
7. Md5 hash to get the signature, signature=md5(path+secret+key_value_pairs)
Compare the request query string sign’s value and the calculated signature in step 7, if it is equal, then we proceed , if not, we response 403 access denied.

With the javascript programming language and the Akamai Edgeworkers build in object  request object , we can implement the Edge Token Authentication Signature successfully to protect our live streaming content.

