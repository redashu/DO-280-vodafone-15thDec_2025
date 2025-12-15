FROM python
# using python latest image if not there any it will pull from docker hub
RUN  mkdir  /ashuapp
# run means giving shell command to the docker image during build time
COPY hello.py /ashuapp/hello.py 
CMD ["python","/ashuapp/hello.py"]
# cmd is used to run the application during container creation time 