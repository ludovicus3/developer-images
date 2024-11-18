FROM quay.io/ludovicus/rails-developer-image:bookworm

USER 0

RUN agt-get install -y graphviz && \
    apt-get clean all

USER 10001
