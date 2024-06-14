В Kafka в отличие от RabbitMQ данные можно прочитать 2 раза, используя offset(смещение по очереди).

Kafka base configuration:

    @Configuration
    public class KafkaConfiguration {

        @Value("${spring.kafka.template.default-topic}")
        private String kafkaTopic;
    
        @Bean
        public NewTopic newTopic() {
    
            return new NewTopic(
                    kafkaTopic, 1, (short) 1
            );
        }
    }

Отправка сообщений.
KafkaTemplate позволяет отправлять сообщения из продюсера:

    @Autowired
    private final KafkaTemplate<Long, String> kafkaTemplate;
    
    public void sendMessage(String msg) {
        kafkaTemplate.send(kafkaTopic, msg);
    }

Если мы попробуем вручную создать объект KafkaTemplate, то увидим, что в конструктор в качестве параметра передаётся объект интерфейса ProducerFactory<K, V>, например DefaultKafkaProducerFactory<>. Для того, чтобы создать DefaultKafkaProducerFactory, нам нужно в его конструктор передать Map, содержащий настройки продюсера. Весь код по конфигурации и созданию продюсера можно вынести в отдельный класс. Для этого создадим пакет config и в нём класс KafkaProducerConfig:

    @Configuration
    public class KafkaProducerConfig {
    
        private String kafkaServer="localhost:9092";
    
        @Bean
        public Map<String, Object> producerConfigs() {
            Map<String, Object> props = new HashMap<>();
            props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
                    kafkaServer);
            props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
                    LongSerializer.class);
            props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
                    StringSerializer.class);
            return props;
        }
    
        @Bean
        public ProducerFactory<Long, String> producerFactory() {
            return new DefaultKafkaProducerFactory<>(producerConfigs());
        }
    
        @Bean
        public KafkaTemplate<Long, String> kafkaTemplate() {
            return new KafkaTemplate<>(producerFactory());
        }
    }

Для десериализации ключа в Consumer по умолчанию используется StringDeserializer, и если мы хотим, чтобы ключ в целочисленном формате корректно отображался, мы должны изменить его на LongDeserializer. Для настройки консьюмера в пакете config создадим класс KafkaConsumerConfig:

    @Configuration
    public class KafkaConsumerConfig {

        @Value("${spring.kafka.bootstrap-servers}")
        private String kafkaServer;
    
        @Value("${spring.kafka.consumer.group-id}")
        private String kafkaGroupId;
    
        @Bean
        public Map<String, Object> consumerConfigs() {
            Map<String, Object> props = new HashMap<>();
            props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, kafkaServer);
            props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, LongDeserializer.class);
            props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
            props.put(ConsumerConfig.GROUP_ID_CONFIG, kafkaGroupId);
            return props;
        }
    
        @Bean
        public KafkaListenerContainerFactory<?> kafkaListenerContainerFactory() {
            ConcurrentKafkaListenerContainerFactory<Long, UserDto> factory =
                    new ConcurrentKafkaListenerContainerFactory<>();
            factory.setConsumerFactory(consumerFactory());
            return factory;
        }
    
        @Bean
        public ConsumerFactory<Long, UserDto> consumerFactory() {
            return new DefaultKafkaConsumerFactory<>(consumerConfigs());
        }
    }
