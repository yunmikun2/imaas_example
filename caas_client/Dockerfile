FROM elixir:1.9.1-alpine as otprelease

RUN apk update && \
    apk upgrade && \
    apk add --no-cache alpine-sdk git openssh build-base && \
    rm -rf /var/cache/apk/*

ENV HOME /app
RUN mkdir $HOME
WORKDIR $HOME

ARG MIX_ENV
ENV MIX_ENV $MIX_ENV

RUN mix local.hex --force && \
    mix local.rebar --force

COPY . .

RUN mix deps.get --only $MIX_ENV && \
    mix compile && \
    mix escript.build

FROM pentacent/alpine-erlang-base:latest as caas_client

ARG CONVERTER_URL
ENV CONVERTER_URL $CONVERTER_URL

COPY --from=bitwalker/alpine-erlang:latest /usr/lib/libcrypto.so.1.1 /usr/lib/libcrypto.so.1.1
COPY --from=otprelease /app/caas_client /bin
