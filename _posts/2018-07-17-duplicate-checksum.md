# Duplicate Checksum

I often relied on checksum() for performing reconciliation tasks until one day I found that for a decimal number, both the positive and the negative value would produce the same checksum. I always knew that checksum() is not perfect for checking differences, but I was still surprised to find how easy it is to produce duplicate checksums.

The following table lists some of my observations.

|Expression|Checksum|
|---|---|
|checksum(1)|1|
|checksum(-1)|-1|
|binary_checksum(1)|1|
|binary_checksum(-1)|-1|
|checksum(1.0)|-1374215283|
|checksum(-1.0)|-1374215283|
|binary_checksum(1.0)|-1374215283|
|binary_checksum(-1.0)|-1374215283|
|checksum('1')|65|
|checksum('-1')|65|
|binary_checksum('1')|49|
|binary_checksum('-1')|737|
|checksum('1.0')|-1043840145|
|checksum('-1.0')|-1043840145|
|binary_checksum('1.0')|13264|
|binary_checksum('-1.0')|189392|

Looking at the table above, I think I will be careful when using checksum() in the future.
