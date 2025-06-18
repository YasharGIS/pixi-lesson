FROM ghcr.io/prefix-dev/pixi:noble AS build

WORKDIR /app
COPY . .
ENV CONDA_OVERRIDE_CUDA=<cuda version>
RUN pixi install --locked --environment <environment>
RUN echo "#!/bin/bash" > /app/entrypoint.sh && \
    pixi shell-hook --environment <environment> -s bash >> /app/entrypoint.sh && \
    echo 'exec "$@"' >> /app/entrypoint.sh

FROM ghcr.io/prefix-dev/pixi:noble AS production

WORKDIR /app
COPY --from=build /app/.pixi/envs/<environment> /app/.pixi/envs/<environment>
COPY --from=build /app/pixi.toml /app/pixi.toml
COPY --from=build /app/pixi.lock /app/pixi.lock
# The ignore files are needed for 'pixi run' to work in the container
COPY --from=build /app/.pixi/.gitignore /app/.pixi/.gitignore
COPY --from=build /app/.pixi/.condapackageignore /app/.pixi/.condapackageignore
COPY --from=build --chmod=0755 /app/entrypoint.sh /app/entrypoint.sh
COPY ./app /app/src

EXPOSE <PORT>
ENTRYPOINT [ "/app/entrypoint.sh" ]
