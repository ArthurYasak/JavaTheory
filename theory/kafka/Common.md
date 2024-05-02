В Kafka в отличие от RabbitMQ данные можно прочитать 2 раза, используя offset(смещение по очереди).

Отправка сообщений.
KafkaTrmplate позволяет отправлять сообщения из продюсера:

    @Autowired
    private final KafkaTemplate<Long, String> kafkaTemplate;
    public void sendMessage(String msg) {
        kafkaTemplate.send(kafkaTopic, msg);
    }

Если мы попробуем вручную создать объект KafkaTemplate, то увидим, что в конструктор в качестве параметра передаётся объект интерфейса ProducerFactory<K, V>, например DefaultKafkaProducerFactory<>. Для того, чтобы создать DefaultKafkaProducerFactory, нам нужно в его конструктор передать Map, содержащий настройки продюсера. Весь код по конфигурации и созданию продюсера можно вынести в отдельный класс. Для этого создадим пакет config и в нём класс KafkaProducerConfig.
