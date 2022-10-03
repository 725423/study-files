# 创建一个Vue项目

- vue --version ：查看vue版本
- nrm ls ：查看安装的东西，镜像等
- node -v ：查看nodejs版本
- vue create 项目名：创建vue项目
- npm run serve：运行项目
- npm i element-ui -S：安装element-ui插件
- TypeScript智能感知在模板中是禁用的，你可以在tsconfig或jsconfig中配置’ “jsx”: "preserve"来启用它，或者配置’ vueCompilerOptions '。 使用experimentalDisableTemplateSupport来禁用这个提示。那么我们根据描述解决问题
- // 在文件 jsconfig.json 中
  {
    "compilerOptions": {
      "baseUrl": "./",
      "paths": {
        "@/*": [
          "src/*"
        ]
      },
      **"jsx": "preserve" //添加配置**
    },
    "exclude": [
      "node_modules",
      "dist"
    ]
  }