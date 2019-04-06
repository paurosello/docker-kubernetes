# Inherit from the Python Docker image
FROM python:3.7-slim
# Install the Flask package via pip
RUN pip install flask==1.0.1
# Copy the source code to app folder
COPY ./server.py /app/
# Change the working directory
WORKDIR /app/
# Set "python" as the entry point
ENTRYPOINT ["python"]
# Expose port
EXPOSE 5000
# Set the command as the script name
CMD ["server.py"]