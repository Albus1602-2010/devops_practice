# 🚀 Задание 3: Пуш Docker-образа в облако

> Цель: Научиться автоматически **публиковать** Docker-образ в удалённый реестр (например, Docker Hub), используя секреты.

Теперь, когда у нас есть рабочий образ, пора **отправить его в облако**, чтобы другие могли его использовать. Как будто загрузить игру в интернет, чтобы все могли её скачать.

Но! Мы не можем писать свой пароль прямо в коде — это опасно!
GitHub позволяет хранить **секреты** — пароли, токены — безопасно.

---

### 🛠️ Что делает файл `publish.yaml`?

```yaml
name: Publish Docker Image
```
> Имя пайплайна.

```yaml
on:
  событие:
    branches: [ имя_ветки ]
```
> Запускается при действии push (событие) в `main` — как и предыдущий.

```yaml
jobs:
  publish:
    runs-on: ubuntu-latest
```
> Выполняется на виртуальной машине.

```yaml
steps:
  - uses: actions/checkout@v4
```
> Скачиваем код.

```yaml
  - name: Log in to Docker Hub
    uses: docker/login-action@v3
    with:
      username: ${{ secrets.REGISTRY_USERNAME }}
      password: ${{ secrets.REGISTRY_PASSWORD }}
```
> 🔐 Авторизация в Docker Hub.
> - `secrets.REGISTRY_USERNAME` и `secrets.REGISTRY_PASSWORD` — **не видны никому**, даже тебе в логах!
> - Они берутся из настроек репозитория: **Settings → Secrets and variables → Actions**. Туда их нужно предварительно добавить. А для этого зарегистрироваться в [Dockerhub](https://hub.docker.com/) или вспомнить логин и пароль, если аккаунт уже есть.  Как добавить секреты, смотри [тут](#-как-добавить-секреты).

```yaml
  - name: Build and Push
    uses: docker/build-push-action@v5
    with:
      context: ./task_2
      file: ./task_2/Dockerfile
      push: true
      tags: ${{ secrets.REGISTRY_USERNAME }}/kitty-app:latest
```
> Этот шаг делает сразу два дела:
> 1. **Собирает** образ из `task_2/Dockerfile`.
> 2. **Пушит** его в registry.
>
> - `context` — папка, откуда брать файлы.
> - `file` — путь к `Dockerfile`.
> - `push: true` — отправить в облако.
> - `tags` — имя образа: `<ваш_логин>/kitty-app:latest`

---

### ✅ Что произойдёт?

1. Код мержится в `main`.
2. Пайплайн запускается.
3. GitHub логинится в Docker Hub (безопасно!).
4. Собирает и отправляет образ.
5. Теперь любой может выполнить:
   ```bash
   docker run ваш_логин/kitty-app:latest
   ```
   — и увидеть котика!

---

### 💡 Почему это важно?

- **Автоматизация**: не надо вручную собирать и пушить.
- **Безопасность**: пароли не в коде.
- **CI/CD в действии**: код → сборка → публикация — всё автоматически!

---

### 🔐 Как добавить секреты?

1. Перейди в репозиторий на GitHub.
2. Нажми `Settings` → `Secrets and variables` → `Actions`.
3. Добавь:
   - Имя: `REGISTRY_USERNAME`, значение: твой логин в Docker Hub
   - Имя: `REGISTRY_PASSWORD`, значение: пароль или токен

> ⚠️ Токен безопаснее пароля! Рекомендуется использовать [Personal Access Token](https://hub.docker.com/settings/security) в Docker Hub.

---

## 🎉 Молодцы!

Теперь вы знаете:
- Как проверять код до мержа.
- Как собирать Docker-образы.
- Как безопасно публиковать их в облаке.

Вы — настоящие DevOps-мастера! 🌟🚀