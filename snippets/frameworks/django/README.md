  Here is to enlist some quick logic for different purposes:


# Production testing

  * test post CSRF work
    ```python
    from django.middleware.csrf import get_token
    def whatname_inline(request):
        html = """
        <html><body>
          <p>What is your name?</p>
          <form method="post" action=".">
             <p> <label>Name:</label> </p>
             <div id="id_name">
                <input type="hidden" name="csrfmiddlewaretoken" value="%s">
                <div> <label for="id_name_0"><input type="radio" name="name" value="jimmy" required="" id="id_name_0"> Jimmy</label> </div>
                <div> <label for="id_name_1"><input type="radio" name="name" value="cedric" required="" id="id_name_1"> Cedric</label> </div>
             </div>
             <input type="submit" value="enter">
          </form>
        </body></html>
        """
        t = get_token(request)
        print(f'{t=}')
        if request.method == "GET":
            html = html % t
            return HttpResponse(html)
        elif request.method == "POST":
            chosen = request.POST['name']
            if chosen == 'cedric': return HttpResponse('Correct')
            else:                return HttpResponse('Wrong')
    ```
  * test upload file
    ```python
    # models.py
    class UploadModel(models.Model):
        upload = models.FileField(upload_to="uploads/")

    # views.py
    from .models import UploadModel
    class UploadFileForm(forms.ModelForm):
        class Meta:
            model = UploadModel
            fields = ["upload"]


    from django.http import HttpResponseRedirect
    def upload_file(request):
        html = """
        <html><body>
          <form method="post" enctype="multipart/form-data">
             <p> <label>Upload File:</label> </p>
             <input type="hidden" name="csrfmiddlewaretoken" value="%s">
             %s
             <input type="submit" value="enter">
          </form>
        </body></html>
        """
        t = get_token(request)
        if request.method == "POST":
            form = UploadFileForm(request.POST, request.FILES)
            if form.is_valid():
                form.save()
                return HttpResponseRedirect("/")
        form = UploadFileForm()
        html = html % (t,form.render())
        return HttpResponse(html)
    ```
  * test send_email
    `don't know why sending does not need port 587...` maybe I somehow mistakenly understood
    ```python
    from django.core.mail import send_mail

    send_mail(
        "Subject here",
        "Here is the message.",
        "from@example.com",
        ["to@example.com"],
        fail_silently=False,
    )
    ```
