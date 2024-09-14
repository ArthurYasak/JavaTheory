##Трехфазовый конструктор
![three_phase_constructor]
(https://github.com/ArthurYasak/JavaTheory/blob/7585d53eaa1a304023a58d1b9be17e2d2ca61b70/images/spring/potroshitel/three_phase_constructor.png)

## BeanFactoryPostProcessor
![Bean_factory_post_processor](https://github.com/ArthurYasak/JavaTheory/blob/df875412ad0f6571b353a4e24ce49057840642d9/images/spring/potroshitel/Bean_factory_post_processor.png)

## ClassPathBeanDefinitionScanner
![Class_path_bean_definition_scanner](https://github.com/ArthurYasak/JavaTheory/blob/89006e0977a4640c1f6324bf50f4401b06c9f132/images/spring/potroshitel/Class_path_bean_definition_scanner.png)

## Как обновлять prototype в singleton'е?
- Заинжектить контекст и из него доставать нужный бин (плохой способ, приводящей к high coupling бина от Spring'а)
- ```java
  @Bean
  @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS) // в конфиге над бином (@Bean) (у меня не сработало: BeanCreationException: Error creating bean with name 'color'; AopConfigException: Could not generate CGLIB subclass of class java.awt.Color)
- Сделать класс, которому нужен бин, абстрактным в нем abstract метод getBean()(напр. getColor()), реализовать этот метод в конфиг классе, передав ему оттуда необходимый синглтон-bean
  ```java
    @Bean
    public ColorFrame colorFrame() {

        return new ColorFrame() {

            @Override
            protected Color getColor() {

                return color(); // not invocation of method, it is handling to bean, and if bean scope is prototype, new color bean creating
            }
        };
    }
  
