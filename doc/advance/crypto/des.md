## Intro

## 原理

## 实现
```java
import javax.crypto.Cipher;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import java.security.AlgorithmParameters;
import java.security.Key;
import java.util.Base64;

public class DES {

    /**
     * 偏移变量，固定占8位字节
     */
    private final static String IV_PARAMETER = "12345678";
    /**
     * 密钥算法
     */
    private static final String ALGORITHM = "DES";
    /**
     * 加密/解密算法-工作模式-填充模式
     */
    private static final String CIPHER_ALGORITHM = "DES/CBC/PKCS5Padding";
    /**
     * 默认编码
     */
    private static final String CHARSET = "utf-8";

    /**
     * 生成key
     *
     * @param key
     * @return
     * @throws Exception
     */
    private static SecretKey generateKey(String key) throws Exception {
        DESKeySpec dks = new DESKeySpec(key.getBytes(CHARSET));
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance(ALGORITHM);
        return keyFactory.generateSecret(dks);
    }

    public static AlgorithmParameters generateIV(byte[] iv) throws Exception {
        AlgorithmParameters params = AlgorithmParameters.getInstance(ALGORITHM);
        params.init(new IvParameterSpec(iv));
        return params;
    }

    /**
     * DES加密字符串
     *
     * @param key 加密密码，长度不能够小于8位
     * @param data 待加密字符串
     * @return 加密后内容
     */
    public static String encrypt(String data, byte[] key, byte[] iv) {
        if (key == null || key.length < 8) {
            throw new RuntimeException("加密失败，key不能小于8位");
        }
        if (data == null)
            return null;
        try {
            SecretKey secretKey = generateKey(new String(key));
            Cipher cipher = Cipher.getInstance(CIPHER_ALGORITHM);
            IvParameterSpec ivParameterSpec = new IvParameterSpec(iv);
            cipher.init(Cipher.ENCRYPT_MODE, secretKey, ivParameterSpec);
            byte[] bytes = cipher.doFinal(data.getBytes(CHARSET));
            return new String(Base64.getEncoder().encode(bytes));

        } catch (Exception e) {  e.printStackTrace(); return data;}
    }

    /**
     * DES解密字符串
     *
     * @param key 解密密码，长度不能够小于8位
     * @param data 待解密字符串
     * @return 解密后内容
     */
    public static String decrypt(String data, byte[] key, byte[] iv) {
        if (key== null || key.length < 8) {
            throw new RuntimeException("加密失败，key不能小于8位");
        }
        if (data == null)
            return null;
        try {
            SecretKey secretKey = generateKey(new String(key));
            Cipher cipher = Cipher.getInstance(CIPHER_ALGORITHM);
            IvParameterSpec ivParameterSpec = new IvParameterSpec(iv);
            cipher.init(Cipher.DECRYPT_MODE, secretKey, ivParameterSpec);
            return new String(cipher.doFinal(Base64.getDecoder().decode(data.getBytes(CHARSET))), CHARSET);
        } catch (Exception e) { e.printStackTrace(); return data;}
    }
}
```

## Reference
* https://blog.csdn.net/gs12software/article/details/83899389