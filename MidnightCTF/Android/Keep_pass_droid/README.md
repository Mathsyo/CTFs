# Description
J'ai créé ma propre application pour android, je vous mets au défi de trouver le mot de passe !

# Write UP
Again, we have an APK file and a little description:

J'ai créé ma propre application pour android, je vous mets au défi de trouver le mot de passe !

### Quick analysis

The given code is a bit more complex. We
 are still trying to recover the password to login as an administrator. 
Here is the most interesting code:

```java
public class LoginDataSource {
    ABS126 c = new ABS126();

    private static String b() {
        StringBuilder stringBuilder1 = new StringBuilder();
        stringBuilder1.append("AQcgGB");
        stringBuilder1.append("pJbn0SNht");
        String str1 = stringBuilder1.toString();
        StringBuilder stringBuilder2 = new StringBuilder();
        stringBuilder2.append("https://");
        stringBuilder2.append("www.youtu");
        String str2 = stringBuilder2.toString();
        StringBuilder stringBuilder3 = new StringBuilder();
        stringBuilder3.append(str2);
        stringBuilder3.append("be.com/watch");
        str2 = stringBuilder3.toString();
        stringBuilder3 = new StringBuilder();
        stringBuilder3.append(str1);
        stringBuilder3.append("lHBZKVQ==");
        str1 = stringBuilder3.toString();
        stringBuilder3 = new StringBuilder();
        stringBuilder3.append(str2);
        stringBuilder3.append("?v=3IWXcFRVmgg");
        str2 = stringBuilder3.toString();
        return b(c(str1), str2);
    }

    private static String b(String paramString1, String paramString2) {
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < paramString1.length(); i++)
            stringBuilder.append((char)(paramString1.charAt(i) ^ paramString2.charAt(i)));
        return stringBuilder.toString();
    }

    private boolean b(String paramString) {
        return ABS126
                .encrypt(paramString, b())
                .equals("GjnphyGDLqgkcf3G84xEPk5tDpHmLI/e5bRv4x1s+mh+vEtX1D2yY1eXZnqCIm+E");
    }

    private static String c(String paramString) {
        return new String(Base64.getDecoder().decode(paramString), StandardCharsets.UTF_8);
    }

    public Result<LoggedInUser> login(String paramString) {
        try {
            if (b(paramString))
                return new Result.Success<LoggedInUser>
                        (new LoggedInUser(UUID.randomUUID().toString(), "Impressive Flagger"));
            throw new Exception("Error.");
        } catch (Exception exception) {
            return new Result.Error(new IOException("Wrong pass", exception));
        }
    }
}
```

Password verification is the same as in 
the previous challenge. However, the string appears to be encrypted with
 the ABS126 class which performs AES-128 ECB encryption. Here the key is
 "given" through a call to the method named b(). I copy the code and run
 it in my IDE which allows me to get the string **isThisAReAlKey?!** which looks like a key.

We have the algorithm used, we have the key and the cipher, so I wrote a Python script to decrypt the flag, here is the code:

```java
from Crypto.Cipher import AES
from base64 import b64decode
import hashlib

result = "GjnphyGDLqgkcf3G84xEPk5tDpHmLI/e5bRv4x1s+mh+vEtX1D2yY1eXZnqCIm+E"
key = b64decode("MN865ADAIOvvswF5Zu50+Q==")

cipher = AES.new(key, AES.MODE_ECB)
flag = cipher.decrypt(result)

print(flag)
```

Yes the value of the key has changed. This is because during encryption of the flag, the key was encoded as follows:

```java
  public static void setKey(String paramString) {
    try {
      key = paramString.getBytes("UTF-8");
      byte[] arrayOfByte = MessageDigest.getInstance("SHA-1").digest(key);
      key = arrayOfByte;
      key = Arrays.copyOf(arrayOfByte, 16);
      secretKey = new SecretKeySpec(key, "AES");
    } catch (NoSuchAlgorithmException noSuchAlgorithmException) {
      noSuchAlgorithmException.printStackTrace();
    } catch (UnsupportedEncodingException unsupportedEncodingException) {
      unsupportedEncodingException.printStackTrace();
      return;
    }
  }
```

To get the new value, I just ran the 
above method displaying the result in base 64 to avoid non-printable 
characters. Finally I run the script and boom, we get the flag.

# Flag
`Flag: MCTF{4ndr01d_b4s1c_cr4ck_m3_ch4ll3ng3_!!}`