FROM python:3.7.4-alpine
LABEL maintainer="Mutation Maker https://github.com/Merck/Mutation_Maker"

RUN apk add --no-cache \
  build-base==0.5-r1 \
  ca-certificates \
  curl

ENV CELERY_BROKER_URL redis://redis:6379/0
ENV CELERY_RESULT_BACKEND redis://redis:6379/0
ENV HOME=/mutation_maker
ENV PRIMER3INSTALL=/primer3
ENV PRIMER3HOME=/primer3/src
ENV PATCHHOME=/patch/

WORKDIR ${PATCHHOME}
RUN wget https://ftp.gnu.org/gnu/patch/patch-2.7.6.tar.gz
RUN tar --strip-components=1 -xvf patch-2.7.6.tar.gz
RUN ./configure
RUN make
RUN cp src/patch /var/tmp/patch

WORKDIR ${PRIMER3INSTALL}
RUN curl -OL https://github.com/primer3-org/primer3/archive/v2.4.0.tar.gz
RUN tar xfz v2.4.0.tar.gz --strip-components=1
RUN rm v2.4.0.tar.gz

COPY primer3_patch /var/tmp/
RUN /var/tmp/patch -p1 < /var/tmp/primer3_patch
WORKDIR ${PRIMER3HOME}

RUN make

COPY requirements.txt /var/tmp/
RUN pip install six==1.11.0 cython==0.27.3
RUN pip install -r /var/tmp/requirements.txt

COPY . ${HOME}
WORKDIR ${HOME}

CMD celery -A tasks worker --loglevel=info
