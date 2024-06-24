FROM alpine:latest

# Set working directory for the application
WORKDIR /usr/local/apache2/htdocs/

# Install Apache
RUN apk add --no-cache apache2 unzip

# Download the covid19.zip file
RUN wget https://linux-devops-course.s3.amazonaws.com/covid19.zip

# Extract the zip file
RUN unzip covid19.zip

# Define volume to persist data (optional)
VOLUME /Saves

# Expose port for Apache
EXPOSE 80

# (Optional) Set CMD to start Apache in foreground
CMD ["httpd", "-D", "FOREGROUND"]
