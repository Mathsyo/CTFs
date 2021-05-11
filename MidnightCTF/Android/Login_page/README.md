# Description
Un lycéen a créé une application où seulement un seul utilisateur peut se connecter, et il a implémenté une vérification "maison".
Curieux, vous avez récupéré l'application, pour voir si vous pouvez vous connecter !

# Write UP
We are given a apk file and a small description:

## App analysis, decompiling

APK archives can be decompiled very simply using several tools. Personally I use two: *apktool & [dex2jar*.](https://github.com/pxb1988/dex2jar)
 Then I open the folder in Java decompiler and quickly find the classes 
that handle authentification (LoginDataSource & LoginRepository). 
The following code immediately caught my attention:

```java
public class LoginDataSource {
    public Result <LoggedInUser> login(String paramString) {
        try {
            LoggedInUser loggedInUser =
                    new LoggedInUser(UUID.randomUUID().toString(), "Admin");
            return (Result <LoggedInUser>)(o(paramString).equals("3476614A6465357265566552743347")
                    ? new Result.Success < LoggedInUser > (loggedInUser)
                    : new Result.Error(new Exception("blep")));
        } catch (Exception exception) {
            return new Result.Error(new IOException("Error logging in", exception));
        }
    }

    public void logout() {}

    public String o(String paramString) {
        String str = "";
        for (int i = paramString.length(); i > 0; i--) {
            String str1 = String.format("%H", Character.valueOf(paramString.charAt(i - 1)));
            StringBuilder stringBuilder = new StringBuilder();
            stringBuilder.append(str);
            stringBuilder.append(str1);
            str = stringBuilder.toString();
        }
        return str;
    }
}
```

The login method instantiates a new 
Admin user and performs a comparison between the input passed in the 'o'
 method and a given string. The 'o' method simply performs a conversion 
to hexadecimal. We therefore conclude that the password is in 
hexadecimal, so we can convert it back to ASCII.

Here is the result: 4vaJde5reVeRt3G. If we reverse the string, we get the flag.

# Flag
`Flag: MCTF{G3tReVer5edJav4}`
 