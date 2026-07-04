# MyBatisCodeHelperPro Crack

## 准备工作

- [JAR 在线反编译](https://www.decompiler.com)
- [Jadx](https://github.com/skylot/jadx)
- [CFR](https://github.com/leibnitz27/cfr)
- [MyBatisCodeHelperPro 插件包](https://plugins.jetbrains.com/plugin/9837-mybatiscodehelperpro/versions/stable)

## 开始处理

### 1. 反编译

> 1. 反编译 JAR 包
> 2. 使用 IDEA 打开反编译后的代码
> 3. 全局检索关键字符串 `TUlHZk1BMEdDU3FHU0liM0RRRUJBUVVBQTRHTkFEQ0JpUUtCZ1FDZzUyUjExV0h1MysvNUV2WnhkS0l2a3ovekpnS2VNUUhNLytMVkxSZS9zWUpFQlUxbUUrODc3MmJJckk4UThscldqSHc5cmVjQ1RWVVhXUnhWYXBndk1HYTZ3KzU4STZwYXdSaFhwZDBrRkhUY2xxeUZGWFpoS3ZiQUtoblphRGNuZkJtSkhObTQwR0JFTGpCTmx5MXpha2FIblFmUzF0QlhaSGQwOUV0c2VRSURBUUFC`
> 4. 正式版的类名和方法名通常经过混淆，需要结合上下文定位，示例如下：

```java
package com.ccnode.codegenerator.validate.utils;

import com.ccnode.codegenerator.validate.response.ValidateNewResultData;
import com.google.gson.Gson;
import java.io.UnsupportedEncodingException;
import java.security.interfaces.RSAPublicKey;
import java.util.Base64;
import kotlin.text.Charsets;

public class RsaUtils {
    private static Gson gson = new Gson();

    public static ValidateNewResultData fromEncrptData(String encrptString) {
        Object var1 = null;

        byte[] res;
        try {
            res = RSAEncrypt.decrypt(RSAEncrypt.loadPublicKeyByStr(new String(Base64.getDecoder().decode("TUlHZk1BMEdDU3FHU0liM0RRRUJBUVVBQTRHTkFEQ0JpUUtCZ1FDZzUyUjExV0h1MysvNUV2WnhkS0l2a3ovekpnS2VNUUhNLytMVkxSZS9zWUpFQlUxbUUrODc3MmJJckk4UThscldqSHc5cmVjQ1RWVVhXUnhWYXBndk1HYTZ3KzU4STZwYXdSaFhwZDBrRkhUY2xxeUZGWFpoS3ZiQUtoblphRGNuZkJtSkhObTQwR0JFTGpCTmx5MXpha2FIblFmUzF0QlhaSGQwOUV0c2VRSURBUUFC"), Charsets.UTF_8)), Base64.getDecoder().decode(encrptString));
        } catch (MyException var7) {
            res = RSAEncrypt.decrypt((RSAPublicKey)(new MyPublicKey()), Base64.getDecoder().decode(encrptString));
        }

        ValidateNewResultData data = null;

        String json;
        try {
            json = new String(res, "UTF-8");
        } catch (UnsupportedEncodingException var6) {
            throw new RuntimeException(var6);
        }

        try {
            data = (ValidateNewResultData)gson.fromJson(json, ValidateNewResultData.class);
            return data;
        } catch (Exception var5) {
            throw new RuntimeException("gson catch exception, the json string is" + json, var5);
        }
    }
}
```

### 2. 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/org.javassist/javassist -->
<dependency>
    <groupId>org.javassist</groupId>
    <artifactId>javassist</artifactId>
    <version>3.30.2-GA</version>
</dependency>
```

> 注：反编译后需要先确认实际的全限定类名和方法名，再替换为对应值。

```java
import javassist.*;
import javassist.bytecode.*;
import java.io.*;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.Comparator;
import java.util.Enumeration;
import java.util.jar.JarEntry;
import java.util.jar.JarFile;
import java.util.stream.Stream;

/**
 * 基于特征字符串的字节码修改器
 * 用于查找并修改 MyBatisCodeHelper-Pro 插件中的特定类，绕过其验证逻辑。
 */
public class MyBatisCodeHelperProCrack {

    // 【特征码】：需要在字节码常量池中搜索的目标字符串（Base64编码的特定特征）
    private static final String TARGET_FEATURE = "TUlHZk1BMEdDU3FHU0liM0RRRUJBUVVBQTRHTkFEQ0JpUUtCZ1FDZzUyUjExV0h1MysvNUV2WnhkS0l2a3ovekpnS2VNUUhNLytMVkxSZS9zWUpFQlUxbUUrODc3MmJJckk4UThscldqSHc5cmVjQ1RWVVhXUnhWYXBndk1HYTZ3KzU4STZwYXdSaFhwZDBrRkhUY2xxeUZGWFpoS3ZiQUtoblphRGNuZkJtSkhObTQwR0JFTGpCTmx5MXpha2FIblFmUzF0QlhaSGQwOUV0c2VRSURBUUFC";

    // 【目标目录】：插件所在的本地目录名称
    private static final String TARGET_DIR_NAME = "MyBatisCodeHelper-Pro";

    public static void main(String[] args) {
        try {
            // 1. 寻找目标 JAR 文件
            File jarFile = findMatchingJar();
            File parentDir = jarFile.getParentFile();
            System.out.println("🎯 [TARGET] 找到目标 JAR: " + jarFile.getName());

            // 2. 清理上一次运行留下的临时文件
            cleanupTempDirectory(parentDir);

            System.out.println("🔍 [ACTION] 正在扫描包含特征字符串的类...\n");

            // 3. 扫描 JAR 并修改符合条件的类
            int modifiedCount = scanAndModifyTargets(jarFile, parentDir.getAbsolutePath());

            System.out.println("\n----------------------------------------");

            // 4. 如果有类被修改，则更新 JAR 包
            if (modifiedCount > 0) {
                updateJarFile(jarFile);
            } else {
                System.out.println("⚠️ [WARN] 未修改任何类，跳过 JAR 更新。");
            }

            // 5. 再次清理临时文件（保持环境整洁）
            cleanupTempDirectory(parentDir);
            System.out.println("🎉 [DONE] 流程执行完毕！");

        } catch (Exception e) {
            System.err.println("❌ [ERROR] 程序异常: " + e.getMessage());
        }
    }

    /**
     * 扫描并修改目标类
     * 逻辑：遍历 JAR 中所有类 -> 查找常量池包含特征字符串的类 -> 查找包含 LDC 指令的方法 -> 替换方法体
     */
    private static int scanAndModifyTargets(File jarFile, String outputDirPath) throws Exception {
        ClassPool pool = ClassPool.getDefault();
        pool.appendClassPath(jarFile.getAbsolutePath());

        JarFile jar = new JarFile(jarFile);
        Enumeration<JarEntry> entries = jar.entries();
        int modifiedCount = 0;

        while (entries.hasMoreElements()) {
            JarEntry entry = entries.nextElement();
            // 只处理 .class 文件
            if (!entry.getName().endsWith(".class")) continue;

            String className = entry.getName().replace("/", ".").replace(".class", "");

            try {
                CtClass ctClass = pool.get(className);
                ClassFile classFile = ctClass.getClassFile();
                ConstPool constPool = classFile.getConstPool();

                // 在常量池中查找特征字符串的索引
                int targetCpIndex = findStringInConstPool(constPool, TARGET_FEATURE);
                // 如果类的常量池中没有该特征，跳过
                if (targetCpIndex == -1) {
                    ctClass.detach();
                    continue;
                }

                System.out.println("📦 [SCAN] 发现特征类: " + className);
                boolean modified = false;

                // 遍历类中的所有方法
                for (MethodInfo methodInfo : classFile.getMethods()) {
                    CodeAttribute codeAttr = methodInfo.getCodeAttribute();
                    if (codeAttr == null) continue;

                    // 【核心匹配逻辑】：只有当方法字节码中真实出现了 LDC 指令加载该特征字符串时，才进行处理
                    // 这能精准定位到验证逻辑的核心方法
                    if (containsLdcInstruction(codeAttr, targetCpIndex)) {
                        String methodName = methodInfo.getName();
                        String descriptor = methodInfo.getDescriptor();

                        // --- 方法过滤逻辑 ---
                        // 1. 必须是静态方法 (static)
                        int accessFlags = methodInfo.getAccessFlags();
                        if ((accessFlags & AccessFlag.STATIC) == 0) {
                            System.out.println(" ⏩ [SKIP] 非static方法: " + methodName + descriptor);
                            continue;
                        }

                        // 2. 参数个数必须 <= 1 (通常用于处理验证Key或JSON输入)
                        CtClass[] paramTypes = Descriptor.getParameterTypes(descriptor, pool);
                        if (paramTypes.length > 1) {
                            System.out.println(" ⏩ [SKIP] 参数个数>1: " + methodName + descriptor);
                            continue;
                        }

                        // --- 逻辑替换准备 ---
                        // 通过方法名和描述符精准查找 CtMethod 对象（解决重载方法的歧义）
                        CtMethod targetCtMethod = null;
                        for (CtMethod ctMethod : ctClass.getDeclaredMethods()) {
                            if (ctMethod.getName().equals(methodName) &&
                                    ctMethod.getMethodInfo2().getDescriptor().equals(descriptor)) {
                                targetCtMethod = ctMethod;
                                break;
                            }
                        }

                        if (targetCtMethod == null) {
                            System.out.println(" ⚠️ [WARN] 未找到匹配的 CtMethod: " + methodName + descriptor);
                            continue;
                        }

                        try {
                            // 生成绕过逻辑的代码并替换原方法体
                            String newBody = generateSmartBypassBody(targetCtMethod, descriptor);
                            targetCtMethod.setBody(newBody);
                            System.out.println(" ✅ [PATCH] 成功拦截并替换: " + methodName + descriptor);
                            System.out.println(" ↳ 注入逻辑: " + newBody);
                            modified = true;
                        } catch (NotFoundException e) {
                            System.out.println(" ⏭️ [SKIP] 缺少外部依赖: " + methodName + " | 缺失: " + e.getMessage());
                        } catch (Exception e) {
                            System.out.println(" ❌ [FAIL] 替换失败: " + methodName + descriptor + " | 原因: " + e.getMessage());
                        }
                    }
                }

                // 如果该类被修改，则写入文件系统（用于后续打包）
                if (modified) {
                    ctClass.writeFile(outputDirPath);
                    modifiedCount++;
                }
                ctClass.detach();
            } catch (Exception e) {
                System.out.println(" ❌ [ERROR] 处理类失败: " + className + " | 原因: " + e.getMessage());
            }
        }

        jar.close();
        System.out.println("\n📊 [SUMMARY] 扫描完成，共精准修改了 " + modifiedCount + " 个类。");
        return modifiedCount;
    }

    /**
     * 在常量池中查找指定字符串
     * @return 字符串在常量池中的索引，未找到返回 -1
     */
    private static int findStringInConstPool(ConstPool cp, String target) {
        for (int i = 1; i < cp.getSize(); i++) {
            if (cp.getTag(i) == ConstPool.CONST_String) {
                try {
                    if (target.equals(cp.getStringInfo(i))) return i;
                } catch (Exception ignored) {}
            }
        }
        return -1;
    }

    /**
     * 检查方法字节码中是否包含加载指定常量池索引的 LDC 指令
     * 这是精准定位“验证逻辑”的关键，避免误伤仅引用了该字符串但不用于验证的类
     */
    private static boolean containsLdcInstruction(CodeAttribute codeAttr, int targetCpIndex) {
        try {
            CodeIterator iter = codeAttr.iterator();
            while (iter.hasNext()) {
                int pos = iter.next();
                int op = iter.byteAt(pos);
                // 检查 LDC, LDC_W, LDC2_W 指令
                if (op == Opcode.LDC) {
                    if (iter.byteAt(pos + 1) == targetCpIndex) return true;
                } else if (op == Opcode.LDC_W || op == Opcode.LDC2_W) {
                    if (iter.u16bitAt(pos + 1) == targetCpIndex) return true;
                }
            }
        } catch (BadBytecode ignored) {}
        return false;
    }

    /**
     * 智能生成绕过逻辑的代码体
     * 根据原方法的返回类型和参数类型，生成不同的返回逻辑
     */
    private static String generateSmartBypassBody(CtMethod method, String descriptor) throws Exception {
        // 解析方法描述符，获取返回类型描述
        String returnDesc = descriptor.substring(descriptor.indexOf(')') + 1);

        // 1. void 返回
        if ("V".equals(returnDesc)) return "{ return; }";

        // 2. boolean 返回 -> 返回 false (失败/无效)
        if ("Z".equals(returnDesc)) return "return false;";

        // 3. 数值类型返回 -> 返回 0
        if ("I".equals(returnDesc) || "J".equals(returnDesc) || "F".equals(returnDesc) ||
                "D".equals(returnDesc) || "B".equals(returnDesc) || "C".equals(returnDesc) || "S".equals(returnDesc)) {
            return "return 0;";
        }

        // 4. 对象引用类型返回
        if (returnDesc.startsWith("L") && returnDesc.endsWith(";")) {
            String internalName = returnDesc.substring(1, returnDesc.length() - 1);
            String fullyQualifiedName = internalName.replace('/', '.');
            CtClass[] paramTypes = method.getParameterTypes();

            // 【智能修复】：如果方法接收一个 String 参数（通常是 JSON 字符串）
            // 则尝试使用 Gson 将其反序列化为期望的对象返回
            // 这通常用于绕过需要返回复杂 LicenseResult 对象的验证
            if (paramTypes.length == 1 && paramTypes[0].getName().equals("java.lang.String")) {
                return "return (" + fullyQualifiedName + ") new com.google.gson.Gson().fromJson($1, " + fullyQualifiedName + ".class);";
            }
            // 其他对象类型直接返回 null
            return "return null;";
        }
        return "return null;";
    }

    /**
     * 寻找目标 JAR 文件
     * 优先在 ~/Downloads/MyBatisCodeHelper-Pro/lib/ 下查找，未找到则在当前目录查找
     */
    private static File findMatchingJar() throws Exception {
        String userHome = System.getProperty("user.home");
        Path basePath = Paths.get(userHome, "Downloads", TARGET_DIR_NAME, "lib");

        if (!Files.exists(basePath) || !Files.isDirectory(basePath)) {
            basePath = Paths.get(".");
        }

        try (Stream<Path> stream = Files.list(basePath)) {
            return stream
                    .filter(path -> path.getFileName().toString().startsWith("instrumented-MyBatisCodeHelper-Pro"))
                    .findFirst()
                    .map(Path::toFile)
                    .orElseThrow(() -> new FileNotFoundException("未找到匹配的 JAR 文件"));
        }
    }

    /**
     * 更新 JAR 文件
     * 调用系统的 jar 命令将修改后的 com 目录更新回原 JAR 包
     */
    private static void updateJarFile(File jarFile) throws Exception {
        File directory = jarFile.getParentFile();
        Path comDir = directory.toPath().resolve("com");

        if (!Files.exists(comDir)) {
            System.out.println("⚠️ [WARN] 未找到修改后的 com 目录，跳过更新。");
            return;
        }

        ProcessBuilder pb = new ProcessBuilder("jar", "uvf", jarFile.getName(), "com");
        pb.redirectErrorStream(true);
        pb.directory(directory);

        System.out.println("🔧 [ACTION] 正在更新 JAR 文件...");
        Process process = pb.start();

        try (BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(" | " + line);
            }
        }

        int exitCode = process.waitFor();
        if (exitCode == 0) {
            System.out.println("✅ [SUCCESS] JAR 文件更新成功！");
        } else {
            System.err.println("❌ [ERROR] JAR 文件更新失败，退出码: " + exitCode);
        }
    }

    /**
     * 清理临时目录
     */
    private static void cleanupTempDirectory(File directory) throws IOException {
        Path tempDir = directory.toPath().resolve("com");
        if (Files.isDirectory(tempDir)) {
            deleteDirectoryRecursively(tempDir.toFile());
        }
    }

    private static void deleteDirectoryRecursively(File directory) throws IOException {
        try (Stream<Path> walkStream = Files.walk(directory.toPath())) {
            walkStream.sorted(Comparator.reverseOrder()).forEach(path -> {
                try {
                    Files.delete(path);
                } catch (IOException ignored) {}
            });
        }
    }
}
```

### 3. 打包安装

> 1. 将最终修改后的目录重新打包为 ZIP 插件包
> 2. 将修改后的插件包安装到 IDEA 中

### 4. 离线激活

- [Download](https://pan.baidu.com/s/1GLvphvvWUhsAY5OPvqLd5g?pwd=0918) ⚠️ 仅供个人学习使用

```text
{
    "paidKey": "xm.z",
    "valid": true,
    "userMac": "${your unique code}",
    "validTo": 4859711999000
}
```
