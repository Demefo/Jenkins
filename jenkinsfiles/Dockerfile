FROM alpine:latest AS builder

# Set working directory for build stage
WORKDIR /tmp

# Install package dependencies
RUN apk add --no-cache unzip wget

# Download the covid19.zip file
RUN wget https://linux-devops-course.s3.amazonaws.com/covid19.zip

# Extract the zip file
RUN unzip covid19.zip

# Copy extracted files to final destination
COPY --from=builder /tmp/covid19/* /usr/local/apache2/htdocs/

# Clean up temporary files
RUN rm -rf /tmp/*

# Switch to the final image stage
FROM alpine:latest

# Set user and group for the application
RUN adduser -D -s /sbin/nologin jane-doe
RUN adduser -D -s /sbin/nologin john-doe

# Set working directory for the application
WORKDIR /usr/local/apache2/htdocs/

# Define volume to persist data
VOLUME /Saves

# Expose ports for Apache
EXPOSE 80-10000

# Copy application files from build stage
COPY --from=builder /usr/local/apache2/htdocs/* .

# Set user and group ownership for application files
RUN chown -R jane-doe:john-doe .

# Expose the CMD (optional)
CMD ["httpd", "-D", "FOREGROUND"]
