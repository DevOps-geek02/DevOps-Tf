FROM ubuntu:22.04 AS extractor
ARG DOWNLOAD_URL=https://github.com/nopSolutions/nopCommerce/releases/download/release-4.60.2/nopCommerce_4.60.2_NoSource_linux_x64.zip
ADD ${DOWNLOAD_URL} /nopCommerce/nopCommerce_4.60.2_NoSource_linux_x64.zip
RUN apt update && apt install unzip -y
RUN cd /nopCommerce && unzip nopCommerce_4.60.2_NoSource_linux_x64.zip && mkdir bin logs && rm nopCommerce_4.60.2_NoSource_linux_x64.zip

FROM mcr.microsoft.com/dotnet/sdk:7.0
LABEL author="aditya"
ARG user=nopcommerce
ARG group=nopcommerce
ARG uid=1000
ARG gid=1000
ARG HOME_DIR=/nop
# Create user nopcommerce
RUN groupadd -g ${gid} ${group} \
    && useradd -d "$HOME_DIR" -u ${uid} -g ${gid} -m -s /bin/bash ${user}

# Copy app files from the extractor stage, ensure App_Data exists and is writable
COPY --from=extractor /nopCommerce ${HOME_DIR}
RUN mkdir -p ${HOME_DIR}/App_Data \
    && chown -R ${user}:${group} ${HOME_DIR} \
    && chmod -R u+rwX ${HOME_DIR}/App_Data

# Run as non-root user
USER ${user}
WORKDIR ${HOME_DIR}
EXPOSE 5000
ENV ASPNETCORE_URLS="http://0.0.0.0:5000"
CMD [ "dotnet", "Nop.Web.dll", "--urls", "http://0.0.0.0:5000"]
