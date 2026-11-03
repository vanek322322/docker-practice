# Сборка
FROM golang:1.22-alpine AS builder

# Используем альтернативный прокси для скачивания зависимостей (чтобы не было i/o timeout)
ENV GOPROXY=https://goproxy.cn,direct

WORKDIR /build

# Сначала копируем все файлы проекта, чтобы go mod tidy видел импорты в main.go
COPY . .

# анализ кода, скачивание зависимостей,создание go.sum, удаление ненужного из go.mod 
RUN go mod tidy

# Собираем статический бинарник
# делаем бинарник статическим(нужно для distroless)
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /out/app . 

# Исполнение (Distroless), минимальный образ без shell и пакетного менеджера, запускаем от non-root пользователя
FROM gcr.io/distroless/static-debian12:nonroot

WORKDIR /app
COPY --from=builder /out/app /app/app

ENTRYPOINT ["/app/app"]