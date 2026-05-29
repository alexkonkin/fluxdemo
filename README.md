# fluxdemo

Этот репозиторий используется как GitOps-источник для локального кластера `kind` с `FluxCD`.

## Bootstrap в GitHub

Для bootstrap репозитория `fluxdemo` использовалась команда:

```bash
flux bootstrap github \
  --token-auth \
  --owner=alexkonkin \
  --repository=fluxdemo \
  --branch=main \
  --path=clusters/fluxcd \
  --private=false \
  --personal=true \
  --components-extra="image-reflector-controller,image-automation-controller"
```

Что делает эта команда:

- создает или обновляет bootstrap-конфигурацию Flux в репозитории GitHub `alexkonkin/fluxdemo`
- использует ветку `main`
- размещает cluster manifests в `clusters/fluxcd`
- настраивает bootstrap для personal GitHub account
- дополнительно устанавливает контроллеры `image-reflector-controller` и `image-automation-controller`

## Зачем добавлены extra controllers

Параметр `--components-extra` нужен для сценариев автоматизации образов:

- `image-reflector-controller` отслеживает теги контейнерных образов в registry
- `image-automation-controller` может обновлять manifests в Git на основе image policies

## Проверка после bootstrap

Проверить, что все контроллеры установлены и работают:

```bash
flux check
kubectl get pods -n flux-system
kubectl get deployments -n flux-system
```

Ожидаемо в `flux-system` должны присутствовать deployment'ы:

- `source-controller`
- `kustomize-controller`
- `helm-controller`
- `notification-controller`
- `image-reflector-controller`
- `image-automation-controller`
