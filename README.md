# sign

Набор GitLab CI/CD шаблонов для подписи репозиториев и артефактов.

---

## sign-core

Шаблон для подписи и аттестации oci репозиториев с проверкой.

### Usage

```yaml
include:
  - component: $CI_SERVER_FQDN/library/cicd/templates/sign/oci-cosign@main
    inputs:
      stage: "sign"
      docker-config-file: "/tmp/config.json"
      oci-image-ref: "$CI_REGISTRY_IMAGE/sign-example:latest"
      cosign-key: "gitlab://$CI_PROJECT_ID"
      cosign-sign-args: "--signing-config='./config.json'"
      cosign-attests-list: "custom|reports/some-file.json"

stages:
  - sign

.sign:oci-cosign:public:env-overwrite:
  - OCI_IMAGE_REF="$[[ inputs.oci-image-ref ]]@SHA..."

.sign:oci-cosign:public:needs:
  - job: some-job

.mirroring:oci-repository:public:rules-extends:
  tags:
    - docker-builder

```

### inputs

| Параметр                  | Описание                                        | Тип     | По умолчанию                     | Обязательный |
|---------------------------|-------------------------------------------------|---------|----------------------------------|--------------|
| oci-sign                  | Включает модуль подписи                         | boolean | true                             | нет          |
| cosign-image              | Версия/образ cosign для загрузки                | string  | docker.io/jitesoft/cosign:v3.0.6 | нет          |
| docker-config-file        | Путь к файлу Docker config.json                 | string  | /root/.docker/config.json        | нет          |
| stage                     | Стадия выполнения job                           | string  | publish                          | нет          |
| oci-image-ref             | OCI образ, который будет подписан               | string  | ""                               | нет          |
| cosign-sign-args          | Дополнительные аргументы для `cosign sign`      | string  | ""                               | нет          |
| cosign-verify-args        | Аргументы для `cosign verify`                   | string  | ""                               | нет          |
| cosign-attest-args        | Аргументы для `cosign attest`                   | string  | ""                               | нет          |
| cosign-verify-attest-args | Аргументы для `cosign verify-attestation`       | string  | ""                               | нет          |
| cosign-key                | Параметр ключа для `cosign sign --key`          | string  | ""                               | нет          |
| cosign-attests-list       | Список аттестаций (slsaprovenance, spdx и т.д.) | string  | ""                               | нет          |
| cosign-annotations        | Дополнительные аннотации для подписи            | string  | "--annotations ..."              | нет          |

### Environment Variables

| Переменная окружения      | Значение                                  | Notes |
|---------------------------|-------------------------------------------|-------|
| COSIGN_IMAGE              | `$[[ inputs.cosign-image ]]`              |       |
| DOCKER_CONFIG_FILE        | `$[[ inputs.docker-config-file ]]`        |       |
| OCI_IMAGE_REF             | `$[[ inputs.oci-image-ref ]]`             |       |
| COSIGN_SIGN_ARGS          | `$[[ inputs.cosign-sign-args ]]`          |       |
| COSIGN_VERIFY_ARGS        | `$[[ inputs.cosign-verify-args ]]`        |       |
| COSIGN_ATTEST_ARGS        | `$[[ inputs.cosign-attest-args ]]`        |       |
| COSIGN_VERIFY_ATTEST_ARGS | `$[[ inputs.cosign-verify-attest-args ]]` |       |
| COSIGN_KEY                | `$[[ inputs.cosign-key ]]`                |       |
| COSIGN_ATTESTS            | `$[[ inputs.cosign-attests-list ]]`       |       |
| COSIGN_ANNOTATIONS        | `$[[ inputs.cosign-annotations ]]`        |       |
| OCI_IMAGE_REF             | `$[[ inputs.oci-image-ref ]]`             |       |

### Template Extension Points

| Расширение / Блок                       | Назначение                            |
|-----------------------------------------|---------------------------------------|
| `.sign:oci-cosign:public:env-overwrite` | Переопределение переменных окружения. |
| `.sign:oci-cosign:public:rules-extends` | Переопределение правил запуска.       |
| `.sign:oci-cosign:public:extends`       | Переопределение блоков job.           |
| `.sign:oci-cosign:public:needs`         | Определяет зависимости от других job. |
