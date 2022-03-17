FROM mcr.microsoft.com/mssql/server:2017-latest

# Install Unzip
RUN apt-get update \
    && apt-get install unzip -y

# Install SQLPackage for Linux and make it executable
RUN wget -progress=bar:force -q -O sqlpackage.zip https://go.microsoft.com/fwlink/?linkid=2134311 \
    && unzip -qq sqlpackage.zip -d /opt/sqlpackage \
    && chmod +x /opt/sqlpackage/sqlpackage

# Add the DACPAC to the image
#COPY ./sql/Membership.Data.SqlServer.dacpac /tmp/memberdb.dacpac
COPY ./infrastructure/sql/telegraf.sql /tmp/telegraf.sql

# Configure external build arguments to allow configurability.
ARG DBNAME=Membership
ARG PASSWORD

# Configure the required environmental variables
ENV ACCEPT_EULA=Y
ENV SA_PASSWORD=YourS3cureP@ass

# Launch SQL Server, confirm startup is complete, deploy the DACPAC, then terminate SQL Server.
# See https://stackoverflow.com/a/51589787/488695
#RUN ( /opt/mssql/bin/sqlservr & ) | grep -q "Service Broker manager has started" \
#    && /opt/sqlpackage/sqlpackage /a:Publish /tsn:. /tdn:Membership /tu:sa /tp:$SA_PASSWORD /sf:/tmp/memberdb.dacpac \
#    && rm /tmp/memberdb.dacpac \
#    && pkill sqlservr

# Launch SQL Server, confirm startup is complete, deploy the script, then terminate SQL Server.
RUN ( /opt/mssql/bin/sqlservr & ) | grep -q "Service Broker manager has started" \
      && /opt/mssql-tools/bin/sqlcmd -i /tmp/telegraf.sql -U sa -P $SA_PASSWORD -l 30 \
      && pkill sqlservr