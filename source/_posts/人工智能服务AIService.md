---
title: 人工智能服务AIService
date: 2025-12-11 15:27:32
tags:
---

#### 一、什么是AIService
AIService使用面向动态代理和接口的方式完成程序的编写，更灵活的实现高级功能的配置。

##### 链Chain
链是针对每个常见的用例都设置一条链，比如聊天机器人和检索增强生成等。链将底层多个组件组合起来并协调他们之间的交互。<b>缺点</b>：不够灵活

##### AIService常见的处理操作
1、为大语言模型格式化输入内容
2、解析大预言模型的输出结果
3、聊天记忆 chat memory
4、工具 tools
5、检索增强生成 RAG


#### 二、创建一个最简单的AIService
##### 1、通过模型注入创建AIService
定义接口`Assistent.java`,包含一个方法chat()
```
public interface Assistent {

    String chat(String prompt);

}
```
调用自定义的chat方法，使用`OpenAiChatModel`
```
@SpringBootTest(classes = XiaozhiApp.class)
public class AIServiceTest {

    @Autowired
    private OpenAiChatModel openAiChatModel;

    @Test
    public void testAIService(){

        Assistent assistent = AiServices.create(Assistent.class, openAiChatModel);
        String response = assistent.chat("请介绍一下你自己。");
        System.out.println("AI Response: " + response);
    }

}
```
执行该测试程序，输出如下：
```
2025-12-11T16:02:00.718+08:00 DEBUG 50590 --- [Http TaskRunner] okhttp3.internal.concurrent.TaskRunner   : Q10005 finished run in 240 µs: OkHttp ConnectionPool
AI Response: 你好！我是通义千问（Qwen），由阿里巴巴集团旗下的通义实验室自主研发的超大规模语言模型。我可以帮助你回答问题、创作文字，比如写故事、写公文、写邮件、写剧本、逻辑推理、编程等等，还能表达观点，玩游戏等。

我支持多种语言，包括但不限于中文、英文、德语、法语、西班牙语等，能够满足国际化的使用需求。同时，我基于大量的互联网文本训练，具备广泛的知识和理解能力。

如果你有任何问题或需要帮助，随时告诉我！😊
```
##### 通过使用@AIService注解
使用@AiService注解来简化注入步骤，直接在接口上增加@AiService注解
```
@AiService
public interface Assistent {

    String chat(String prompt);

}
```
调用模型时不再注入模型到上下文中
```
@Autowired
private Assistent assistent;

@Test
public void testAIServiceWithSpring(){
    String response = assistent.chat("请介绍一下我自己。");
    System.out.println("AI Response: " + response);
}
```
此时如果上下文中有多个模型则会运行报错：
`Caused by: dev.langchain4j.service.IllegalConfigurationException: Conflict: multiple beans of type dev.langchain4j.model.chat.ChatLanguageModel are found: [ollamaChatModel, openAiChatModel]. Please specify which one you wish to wire in the @AiService annotation like this: @AiService(wiringMode = EXPLICIT, chatModel = "<beanName>").`,需要在@AiService中明确执行使用哪一个模型，修改接口如下:
```
@AiService(wiringMode = EXPLICIT, chatModel = "openAiChatModel")
public interface Assistent {

    String chat(String prompt);

}
```
再次重新运行则正常输出相应内容
```
2025-12-11T16:01:08.799+08:00 DEBUG 50574 --- [Http TaskRunner] okhttp3.internal.concurrent.TaskRunner   : Q10005 finished run in 189 µs: OkHttp ConnectionPool
AI Response: 你好！不过我还不太了解你，所以无法准确地介绍你。如果你愿意告诉我一些关于你的信息——比如你的兴趣爱好、职业、学习背景、所在地区，或者你希望别人怎么看待你——我很乐意根据你提供的内容，帮你写一段个性化的自我介绍！😊
```
