"=========== Meta ============
"StrID : 65
"Title : The most simple way to crack atlassian
"Slug  : atlassian-crack
"Cats  : Uncategorized
"Tags  : 
"=============================
"EditType   : post
"EditFormat : Markdown
"BlogAddr   : http://blog.pkufranky.com/
"========== Content ==========
# Decompile `atlassian-extras-<version>.jar`

| Product    | Directory ATLASSIAN-EXTRAS-`<VERSION>`.JAR |
|:-----------|:-------------------------------------------|
| confluence | `<INSTALL_DIR>`/confluence/WEB-INF/lib     |
| jira	     | `<INSTALL_DIR>`/atlassian-jira/WEB-INF/lib |
| crowd	     | `<INSTALL_DIR>`/crowd-webapp/WEB-INF/lib   |

Also in the corresponding product installation directory with the following order to find

	find . -name 'atlassian-extras*'

下面均假设该文件version为1.15，即`atlassian-extras-1.15.jar`，使用jad反编译

	# Decompress
	jar xf atlassian-extras-1.15.jar
	# Decompile x.class from x.java
	jad -o -r -sjava 'com/**/*.class'

# Sourcecode editing

Patch a product

confluence (atlassian-extras-1.15.jar)

	--- a/com/atlassian/license/applications/confluence/ConfluenceLicenseTypeStore.java
	+++ b/com/atlassian/license/applications/confluence/ConfluenceLicenseTypeStore.java
	@@ -44,11 +44,11 @@ public class ConfluenceLicenseTypeStore extends LicenseTypeStore
		 }

		 public static LicenseType ACADEMIC = new DefaultLicenseType(16, "Confluence: Academic", false, true);
	-    public static LicenseType EVALUATION = new DefaultLicenseType(32, "Confluence: Evaluation", true, true);
	+    public static LicenseType EVALUATION = new DefaultLicenseType(85, "Confluence: Evaluation", true, true);
		 public static LicenseType TESTING = new DefaultLicenseType(48, "Confluence: Testing", true, true, true);
		 public static LicenseType HOSTED_EVALUATION = new DefaultLicenseType(64, "Confluence: Hosted Evaluation", true, true);
		 public static LicenseType NON_PROFIT = new DefaultLicenseType(78, "Confluence: Non-Profit / Open Source", false, true);
	-    public static LicenseType FULL_LICENSE = new DefaultLicenseType(85, "Confluence: Commercial Server", false, true);
	+    public static LicenseType FULL_LICENSE = new DefaultLicenseType(32, "Confluence: Commercial Server", false, true);

jira TBD

crowd (atlassian-extras-1.10.jar)

	--- a/com/atlassian/license/applications/crowd/CrowdLicenseTypeStore.java
	+++ b/com/atlassian/license/applications/crowd/CrowdLicenseTypeStore.java
	@@ -39,9 +39,9 @@ public class CrowdLicenseTypeStore extends LicenseTypeStore
		 }

		 public static LicenseType CROWD_ACADEMIC = new DefaultLicenseType(601, "Crowd: Academic", false, true);
	-    public static LicenseType CROWD_COMMERCIAL = new DefaultLicenseType(609, "Crowd: Commercial", false, true);
	+    public static LicenseType CROWD_COMMERCIAL = new DefaultLicenseType(625, "Crowd: Commercial", false, true);
		 public static LicenseType CROWD_COMMUNITY = new DefaultLicenseType(617, "Crowd: Community", false, true);
	-    public static LicenseType CROWD_EVALUATION = new DefaultLicenseType(625, "Crowd: Evaluation", true, true);
	+    public static LicenseType CROWD_EVALUATION = new DefaultLicenseType(609, "Crowd: Evaluation", true, true);
		 public static LicenseType CROWD_OPEN_SOURCE = new DefaultLicenseType(633, "Crowd: Open Source", false, true);
		 public static LicenseType CROWD_DEVELOPER = new DefaultLicenseType(641, "Crowd: Developer", false, true);
		 public static LicenseType CROWD_DEMONSTRATION = new DefaultLicenseType(649, "Crowd: Demonstration", false, true);

# Compile and overwrite the original class in jar file, and copy the jar back to installation directory
 
confluence

	CLASSPATH=atlassian-extras-1.15.jar javac com/atlassian/license/applications/confluence/ConfluenceLicenseTypeStore.java
	jar -uf atlassian-extras-1.15.jar com/atlassian/license/applications/confluence/ConfluenceLicenseTypeStore.class
	cp -fv atlassian-extras-1.15.jar <INSTALL_DIR>/confluence/WEB-INF/lib/

crowd

	CLASSPATH=atlassian-extras-1.10.jar javac com/atlassian/license/applications/crowd/CrowdLicenseTypeStore.java
	jar -uf atlassian-extras-1.10.jar com/atlassian/license/applications/crowd/CrowdLicenseTypeStore.class
	cp -fv atlassian-extras-1.10.jar <INSTALL_DIR>/crowd-webapp/WEB-INF/lib/




# Patching com/atlassian/extras/decoder/v2/Version2LicenseDecoder.java

    private byte[] checkAndGetLicenseText(String string) {
        DataInputStream dataInputStream = new DataInputStream(new ByteArrayInputStream(Base64.decodeBase64((byte[])string.getBytes())));
        int n = dataInputStream.readInt();
        byte[] arrby = new byte[n];
        dataInputStream.read(arrby);
        return arrby;
    }



    static {
        try {
            String pubKeyEncoded = "MIHwMIGoBgcqhkjOOAQBMIGcAkEA/KaCzo4Syrom78z3EQ5SbbB4sF7ey80etKII864WF64B81uRpH5t9jQTxeEu0ImbzRMqzVDZkVG9xD7nN1kuFwIVAJYu3cw2nLqOuyYO5rahJtk0bjjFAkBnhHGyepz0TukaScUUfbGpqvJE8FpDTWSGkx0tFCcbnjUDC3H9c9oXkGmzLik1Yw4cIGI1TQ2iCmxBblC+eUykA0MAAkBrKJN92XEUFWggagAhhhNtFVc/Nh/JTnB3xsQ5azfHq7UcFtPEq0ohc3vGZ7OGEQS7Ym08DB6B1DtD93CwaNdX";
            KeyFactory keyFactory = KeyFactory.getInstance("DSA");
            PUBLIC_KEY = keyFactory.generatePublic(new X509EncodedKeySpec(Base64.decodeBase64((byte[])"MIHwMIGoBgcqhkjOOAQBMIGcAkEA/KaCzo4Syrom78z3EQ5SbbB4sF7ey80etKII864WF64B81uRpH5t9jQTxeEu0ImbzRMqzVDZkVG9xD7nN1kuFwIVAJYu3cw2nLqOuyYO5rahJtk0bjjFAkBnhHGyepz0TukaScUUfbGpqvJE8FpDTWSGkx0tFCcbnjUDC3H9c9oXkGmzLik1Yw4cIGI1TQ2iCmxBblC+eUykA0MAAkBrKJN92XEUFWggagAhhhNtFVc/Nh/JTnB3xsQ5azfHq7UcFtPEq0ohc3vGZ7OGEQS7Ym08DB6B1DtD93CwaNdX".getBytes())));
        }
        catch (NoSuchAlgorithmException e) {
            throw new Error(e);
        }
        catch (InvalidKeySpecException e) {
            throw new Error(e);
        }
    }
