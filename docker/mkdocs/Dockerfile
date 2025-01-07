FROM python:3.9-alpine
ARG additional_libraries
WORKDIR /app
RUN pip install mkdocs $additional_libraries
ENTRYPOINT ["mkdocs"]
