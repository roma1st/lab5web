# [Розгортання Веб] Лабораторна робота №5

## Тема
Впровадження CI/CD: автоматизація перевірок коду та публікація образів у GitHub Actions.

## Титульна сторінка
- Дисципліна: Розгортання Веб
- Лабораторна робота: №5
- ПІБ студента: ____________________
- Група: ____________________
- Дата: 30.03.2026

## Мета роботи
Практично опанувати GitHub Actions для автоматичного контролю якості коду (lint + unit tests) та публікації Docker-образу у GitHub Container Registry (GHCR).

## Завдання
1. Налаштувати середовище тестування Jest.
2. Створити базові unit-тести.
3. Підготувати Dockerfile з multi-stage build для Next.js standalone.
4. Реалізувати CI/CD workflow у GitHub Actions.
5. Налаштувати Branch Protection Rules для main.

## Посилання на репозиторій
Вставити посилання: ____________________

## Реалізація

### 1. Інтеграція Jest
- Встановлені пакети:

```bash
npm install -D jest jest-environment-jsdom @types/jest @testing-library/react @testing-library/jest-dom
```

- Додані файли:
1. `jest.config.js`
2. `jest.setup.ts`
3. `__tests__/index.test.tsx`

- Доданий скрипт у `package.json`:

```json
"test": "jest",
"test:ci": "jest --ci --coverage --watchAll=false"
```

### 2. Docker-інфраструктура
- Налаштовано `next.config.ts`:

```ts
output: "standalone"
```

- Додані файли:
1. `Dockerfile` (multi-stage)
2. `.dockerignore`

### 3. CI/CD у GitHub Actions
- Додано workflow: `.github/workflows/ci-cd.yml`
- Job `quality-checks`:
1. `npm ci`
2. `npm run lint`
3. `npm run test:ci`

- Job `build-and-publish`:
1. `docker/setup-buildx-action`
2. `docker/login-action` (GHCR)
3. `docker/metadata-action`
4. `docker/build-push-action`

## Команди для локальної перевірки

```bash
npm run lint
npm run test
npm run test:ci
npm run build
docker build -t lab5-local:latest .
docker run --rm -p 3000:3000 lab5-local:latest
```

## Що обов'язково заскрінити (строго за вимогами)
1. **Actions: quality-checks для Pull Request**
   - Скрін екрана зі вкладки Actions.
   - Має бути видно, що запущено подією `pull_request` і job `quality-checks` успішний.

2. **Actions: build-and-publish після merge у main**
   - Скрін екрана зі вкладки Actions.
   - Має бути видно, що запуск подією `push` у `main` і job `build-and-publish` успішний.

3. **Packages: опублікований Docker-образ**
   - Скрін вкладки Packages з пакетом `ghcr.io/<username>/<repo>`.
   - Видно тег `latest` та/або SHA-тег.

4. **Локальна перевірка docker pull**
   - Виконайте команду:

```bash
docker pull ghcr.io/<username>/<repo>:latest
```

   - Скрін терміналу з успішним `Status: Downloaded newer image` або `Image is up to date`.

## Налаштування Branch Protection Rules (main)
1. `Settings` -> `Branches` -> `Add branch protection rule`.
2. `Branch name pattern`: `main`.
3. Увімкнути `Require status checks to pass before merging`.
4. Обрати статус `quality-checks`.
5. Увімкнути `Require pull request reviews before merging` (рекомендовано).
6. За потреби увімкнути `Do not allow bypassing the above settings`.

## Висновки
У роботі реалізовано повний CI/CD цикл: автоматизовані перевірки якості коду (lint + unit tests), контейнеризація застосунку через оптимізований multi-stage Dockerfile та публікація артефакту в GHCR. Підхід зменшує ризики людських помилок, забезпечує повторюваність збірок і створює фундамент для подальшого розширення до повного CD/GitOps.