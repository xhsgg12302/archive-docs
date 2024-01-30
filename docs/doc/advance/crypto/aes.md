## Intro

## 原理

## 实现
```java
import org.bouncycastle.jce.provider.BouncyCastleProvider;

import javax.crypto.Cipher;
import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import java.security.AlgorithmParameters;
import java.security.SecureRandom;
import java.security.Security;
import java.util.Base64;

public class AES {

    //加密方式
    private static final String ALGORITHM = "AES";
    //数据填充方式
    private static final String CIPHER_ALGORITHM = "AES/CBC/PKCS7Padding";
    /**
     * 默认编码
     */
    private static final String CHARSET = "utf-8";

    //避免重复new生成多个BouncyCastleProvider对象，因为GC回收不了，会造成内存溢出
    //只在第一次调用decrypt()方法时才new 对象
    public static boolean initialized = false;

    static { Security.addProvider(new BouncyCastleProvider());}

    public static byte[] generateKey() throws Exception {
        KeyGenerator keyGenerator = KeyGenerator.getInstance(ALGORITHM);
        SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG", "SUN");
        secureRandom.setSeed((CIPHER_ALGORITHM + "NEW_BRUSH").getBytes());
        keyGenerator.init(256, secureRandom);//要生成多少位，只需要修改这里即可128, 192或256
        SecretKey originalKey = keyGenerator.generateKey();
        return originalKey.getEncoded();
    }

    public static AlgorithmParameters generateIV(byte[] iv) throws Exception {
        AlgorithmParameters params = AlgorithmParameters.getInstance("AES");
        params.init(new IvParameterSpec(iv));
        return params;
    }

    /**
     * 加密
     */
    public static String encrypt(String content, byte[] key, byte[] iv) {
        try {
            byte[] originalContent = content.getBytes(CHARSET);
            Cipher cipher = Cipher.getInstance(CIPHER_ALGORITHM);
            SecretKeySpec skeySpec = new SecretKeySpec(key, ALGORITHM);
            cipher.init(Cipher.ENCRYPT_MODE, skeySpec, new IvParameterSpec(iv));
            byte[] encrypted = cipher.doFinal(originalContent);
            return Base64.getEncoder().encodeToString(encrypted);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * AES解密
     * 填充模式AES/CBC/PKCS7Padding
     * 解密模式128
     * @return 目标密文
     */
    public static String decrypt(String content, byte[] key, byte[] iv) {
        try {
            byte[] originalContent = Base64.getDecoder().decode(content);
            Cipher cipher = Cipher.getInstance(CIPHER_ALGORITHM);
            SecretKey sKeySpec = new SecretKeySpec(key, ALGORITHM);
            cipher.init(Cipher.DECRYPT_MODE, sKeySpec, generateIV(iv));// 初始化
            byte[] result = cipher.doFinal(originalContent);
            return new String(result,CHARSET);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

## Reference
* https://github.com/12302-msb/newbrush-app/blob/main/newbrush-common/src/main/java/com/newbrush/common/utils/AesUtils.java
* https://github.com/12302-msb/newbrush-app/commit/02787ada6feb5e4643db196a00f1cd994868a2e1