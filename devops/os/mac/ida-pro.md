---
---

* ## Intro(IDA Pro | ida-pro | 9.2)
    
    > [?] 从网盘下载压缩包 [ida-pro_92_x64mac.app.zip](https://pan.quark.cn/s/21f13b2fff94#/list/share/259bbcc078434c43931c7e62d4e2708d)，解压并安装。
    
    ![](/.images/devops/os/mac/ida-pro/ida-92-setup-01.png ':size=40%') ![](/.images/devops/os/mac/ida-pro/ida-92-setup-02.png ':size=40%')

    > [?] 将下的脚本写入 **idakeygen.py** 文件中，并在目录 **/Applications/IDA Professional 9.2.app/Contents/MacOS** 下面运行`python idakeygen.py`，生成 license。

    <details><summary>idakeygen.py</summary>

    ```python [data-file:idakeygen.py]
    # -*- coding: utf-8 -*-

    import json
    import hashlib
    import os
    import platform
    
    license = {
        "header": {"version": 1},
        "payload": {
            "name": "IDAPRO9",
            "email": "idapro9@example.com",
            "licenses": [
                {
                    "id": "48-2137-ACAB-99",
                    "edition_id": "ida-pro",
                    "description": "license",
                    "license_type": "named",
                    "product": "IDA",
                    "product_id": "IDAPRO",
                    "product_version": "9.2",
                    "seats": 1,
                    "start_date": "2024-08-10 00:00:00",
                    "end_date": "2033-12-31 23:59:59",
                    "issued_on": "2024-08-10 00:00:00",
                    "owner": "HexRays",
                    "add_ons": [],
                    "features": [],
                }
            ],
        },
    }
    
    def add_every_addon(license):
        platforms = [
            "W",  # Windows
            "L",  # Linux
            "M",  # macOS
        ]
        addons = [
            "HEXX86",
            "HEXX64",
            "HEXARM",
            "HEXARM64",
            "HEXMIPS",
            "HEXMIPS64",
            "HEXPPC",
            "HEXPPC64",
            "HEXRV64",
            "HEXARC",
            "HEXARC64",
        ]
    
        i = 0
        for addon in addons:
            i += 1
            license["payload"]["licenses"][0]["add_ons"].append(
                {
                    "id": f"48-1337-0000-{i:02}",
                    "code": addon,
                    "owner": license["payload"]["licenses"][0]["id"],
                    "start_date": "2024-08-10 00:00:00",
                    "end_date": "2033-12-31 23:59:59",
                }
            )
        
    add_every_addon(license)
    
    def json_stringify_alphabetical(obj):
        return json.dumps(obj, sort_keys=True, separators=(",", ":"))
    
    def buf_to_bigint(buf):
        return int.from_bytes(buf, byteorder="little")
    
    def bigint_to_buf(i):
        return i.to_bytes((i.bit_length() + 7) // 8, byteorder="little")
    
    # Yup, you only have to patch 5c -> cb in libida64.so
    pub_modulus_hexrays = buf_to_bigint(
        bytes.fromhex(
            "edfd425cf978546e8911225884436c57140525650bcf6ebfe80edbc5fb1de68f4c66c29cb22eb668788afcb0abbb718044584b810f8970cddf227385f75d5dddd91d4f18937a08aa83b28c49d12dc92e7505bb38809e91bd0fbd2f2e6ab1d2e33c0c55d5bddd478ee8bf845fcef3c82b9d2929ecb71f4d1b3db96e3a8e7aaf93"
        )
    )
    pub_modulus_patched = buf_to_bigint(
        bytes.fromhex(
            "edfd42cbf978546e8911225884436c57140525650bcf6ebfe80edbc5fb1de68f4c66c29cb22eb668788afcb0abbb718044584b810f8970cddf227385f75d5dddd91d4f18937a08aa83b28c49d12dc92e7505bb38809e91bd0fbd2f2e6ab1d2e33c0c55d5bddd478ee8bf845fcef3c82b9d2929ecb71f4d1b3db96e3a8e7aaf93"
        )
    )
    
    private_key = buf_to_bigint(
        bytes.fromhex(
            "77c86abbb7f3bb134436797b68ff47beb1a5457816608dbfb72641814dd464dd640d711d5732d3017a1c4e63d835822f00a4eab619a2c4791cf33f9f57f9c2ae4d9eed9981e79ac9b8f8a411f68f25b9f0c05d04d11e22a3a0d8d4672b56a61f1532282ff4e4e74759e832b70e98b9d102d07e9fb9ba8d15810b144970029874"
        )
    )
    
    def decrypt(message):
        decrypted = pow(buf_to_bigint(message), exponent, pub_modulus_patched)
        decrypted = bigint_to_buf(decrypted)
        return decrypted[::-1]
    
    def encrypt(message):
        encrypted = pow(buf_to_bigint(message[::-1]), private_key, pub_modulus_patched)
        encrypted = bigint_to_buf(encrypted)
        return encrypted
    
    exponent = 0x13
    
    def sign_hexlic(payload: dict) -> str:
        data = {"payload": payload}
        data_str = json_stringify_alphabetical(data)
    
        buffer = bytearray(128)
        # first 33 bytes are random
        for i in range(33):
            buffer[i] = 0x42
    
        # compute sha256 of the data
        sha256 = hashlib.sha256()
        sha256.update(data_str.encode())
        digest = sha256.digest()
    
        # copy the sha256 digest to the buffer
        for i in range(32):
            buffer[33 + i] = digest[i]
    
        # encrypt the buffer
        encrypted = encrypt(buffer)
    
        return encrypted.hex().upper()
    
    def patch(filename):
        if not os.path.exists(filename):
            print(f"Skip: {filename} - didn't find")
            return
    
        with open(filename, "rb") as f:
            data = f.read()
    
            if data.find(bytes.fromhex("EDFD42CBF978")) != -1:
                print(f"Patch: {filename} - looks to be already patched :)")
                return
    
            if data.find(bytes.fromhex("EDFD425CF978")) == -1:
                print(f"Patch: {filename} - doesn't contain the original modulus.")
                return
    
            data = data.replace(
                bytes.fromhex("EDFD425CF978"), bytes.fromhex("EDFD42CBF978")
            )
        
        with open(filename, "wb") as f:
            f.write(data)

        print(f"Patch: {filename} - OK")
    
    license["signature"] = sign_hexlic(license["payload"])
    serialized = json_stringify_alphabetical(license)
    
    filename = "idapro.hexlic"
    with open(filename, "w") as f:
        f.write(serialized)
    
    print(f"\nSaved new license to {filename}!\n")
    
    os_name = platform.system().lower()
    if os_name == 'windows':
        patch("ida.dll")
        patch("ida32.dll")
    elif os_name == 'linux':
        patch("libida.so")
        patch("libida32.so")
    elif os_name == 'darwin':
        patch("libida.dylib")
        patch("libida32.dylib")
    ```
    </details>

    ![](/.images/devops/os/mac/ida-pro/ida-92-runtime-01.png ':size=70%')

    + ### 插件安装

        1. #### binexport
            
            根据 README 编译 [binexport](https://github.com/google/binexport) 。需要注意的是，在 macOS 上用 IDA SDK 9.2 编译时，CMake 往往会报 __ld: library not found for -lida64__ 错误。这是因为旧版的 FindIdaSdk.cmake 无法正确识别 IDA 9 引入的新动态库结构 [参考 issue:159](https://github.com/google/binexport/issues/159)。还有需要 [ida-sdk-v92](https://github.com/HexRaysSA/ida-sdk/releases/tag/v9.2)，下载对应的配置好编译就行，一般在 **build/ida/** 里面就可以找到刚才生成的共享库 [binexport12_ida64.dylib](https://github.com/xhsgg12302/archive-assets/blob/044e381f5d1878072e75d65434ee9b5302503800/devops/os/mac/ida-pro/binexport12_ida64.dylib)

        2. #### bindiff

            根据 README 编译 [bindiff](https://github.com/google/bindiff.git) 。这个编译的过程和上面那个基本一致，不过除了需要 ids-sdk 之外，还依赖上一个 binexport，最终生成 [bindiff8_ida64.dylib](https://github.com/xhsgg12302/archive-assets/blob/044e381f5d1878072e75d65434ee9b5302503800/devops/os/mac/ida-pro/bindiff8_ida64.dylib)

        3. #### Built-in 内建插件

            拉取代码：`git clone --depth 1 --single-branch  -b v9.2.0-sdk.1 --recurse-submodules https://github.com/HexRaysSA/ida-sdk.git`
            <br>根据 README 编译 [ida-sdk,tag:v9.2.0-sdk.1](https://github.com/HexRaysSA/ida-sdk.git)。按照[文档提示](https://github.com/HexRaysSA/ida-sdk/blob/v9.2.0-sdk.1/README.md#run) 可以复制`src/bin/plugins/*` 到`/Applications/IDA Professional 9.2.app/Contents/MacOS/plugins/`里面，或者用户环境 `~/.idapro/plugins/`里面，这里我选择在`~/.idapro/`目录里面挂软链`ln -sf /Users/stevenobelia/Documents/project_clion_test/ida-sdk/src/bin/plugins plugins`。

            ![](/.images/devops/os/mac/ida-pro/ida-92-plugins-01.png ':size=70%')

        4. #### 通过 hcli 安装插件仓库里面的

* ## Reference

    + https://ycc77.com/2025/09/04/15-IDA_PRO_9.2%E7%A0%B4%E8%A7%A3%E7%89%88/
    + https://mrx.hk/posts/0f4e4b9537a2da059095327c45b5b227/
    + https://xclient.info/s/ida.html
    + https://github.com/google/binexport/issues/159
    + https://github.com/HexRaysSA/ida-sdk/releases/tag/v9.2
    + 
    + 插件
    + https://hcli.docs.hex-rays.com/   # 插件管理客户端 HCLI,hcli
    + https://plugins.hex-rays.com/     # 官方插件库