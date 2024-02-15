
* ## Intro

    ?> 常见的 对称加密 算法主要有 [DES](./des.md)、3DES、[AES](./aes.md) 等，常见的 非对称算法 主要有 [RSA](./rsa.md)、DSA、ECC，散列算法 主要有 SHA-1、MD5 等。[代码实现参考](https://github.com/12302-bak/idea-test-project/tree/learning/_0_base-learning/src/main/java/_base/encryption)

* ## Key

    + ### 生成自定义key

        ?> 使用ascii生成可以的方法如下：

        ```java
        public static byte[] getRandomKey(){
            int len = 16; // 128 ->16  256->32
            byte[] keys = new byte[len];
            Random rdom = new Random();
            for (int i = 0; i < len; i++) {
                keys[i] = (byte) (rdom.nextInt(126-34) +34);
            }
            return keys;
        }
        // kZZ`MbMLbp5sc7Y'
        // U2yT4Q)2Ss+Li=Y)
        // "OAKJH5kKKLq?g'p
        // h&GKS.gAG,H_@et%
        // .]?]_8ECB[Z'gYn)
        // <mJ\J_PlLE][_uI9
        // 2j=HC,#Au}VDE\P:
        // 27Fqc/j6LODd<.8O
        // Gtt17Q\IyFrNtthk
        // 4q>?A{b|mo@kqM2Q
        ```

        ![](/.images/doc/advance/crypto/crypto-ascii-01.png ':size=70%')

    + ### 使用`SecretKeyFactory`构造

        ```java
        private static SecretKey getKey(String key) throws Exception {
            DESKeySpec dks = new DESKeySpec(key.getBytes("utf-8"));
            SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
            return keyFactory.generateSecret(dks);
        }
        // split .can direct use
        private static SecretKey getKey(byte[] key){
            return new SecretKeySpec(key, "AES");
        }
        ```
    
    + ### 使用`KeyGenerator`生成全新的

        ```java
        public static byte[] getRandomKey() throws Exception {
            KeyGenerator keyGenerator = KeyGenerator.getInstance("AES");
            SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG", "SUN");
            secureRandom.setSeed(("AES/CBC/PKCS7Padding" + "EVERY-seed").getBytes());
            keyGenerator.init(256, secureRandom); //要生成多少位，只需要修改这里即可128, 192或256
            SecretKey originalKey = keyGenerator.generateKey();
            return originalKey.getEncoded(); // byte[32]
        }
        ```

    + Reference

        - https://stackoverflow.com/questions/26295437/should-i-use-keygenerator-or-secretkeyfactory
        - https://www.johndcook.com/blog/2022/05/28/how-to-memorize-the-ascii-table/

* ## Test

    + ### DES
    
        ```java
        @Test
        public void testDES(){

            String key = "U2yT4Q)2Ss+Li=Y)";
            String iv  = "2j=HC,#Au}VDE\\P:";
            iv  = "2=H,#Au}"; //des 8 byte
            String data = "hello world from 加密";

            String encrypt = DES.encrypt(data, key.getBytes(), iv.getBytes());
            System.out.println(encrypt);
            String decrypt = DES.decrypt(encrypt, key.getBytes(), iv.getBytes());
            System.out.println(decrypt);

        }
        ```

        ![](/.images/doc/advance/crypto/crypto-des-01.png ':size=70%')

    + ### AES

        ```java
        @Test
        public void testAES(){
            String key = "U2yT4Q)2Ss+Li=Y)";
            String iv  = "2j=HC,#Au}VDE\\P:";
            //iv  = "2=H,#Au}"; //des 8 byte
            String data = "hello world from 加密";

            String encrypt = AES.encrypt(data, key.getBytes(), iv.getBytes());
            System.out.println(encrypt);
            String decrypt = AES.decrypt(encrypt, key.getBytes(), iv.getBytes());
            System.out.println(decrypt);
        }
        ```

        ![](/.images/doc/advance/crypto/crypto-aes-01.png ':size=70%')

* ## Reference

    + https://juejin.cn/post/6844903638117122056
    + https://stackoverflow.com/questions/140131/convert-a-string-representation-of-a-hex-dump-to-a-byte-array-using-java
    + [Wrong key size](https://www.cnblogs.com/jying/p/9511247.html)
    + [Wrong IV length: must be 8 bytes long](https://crypto.stackexchange.com/questions/76845/does-the-initialization-vector-in-des-have-to-be-8-bytes-long)