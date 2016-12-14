---
layout: post
title: "Extending Django User Model"
description: "Django의 유저모델을 확장해보자"
date: 2016-12-14
tags: []
comments: true
share: true
---

Django의 기본 유저 모델을 확장하여, 이메일을 아이디로 사용하고, 유저마다 닉네임과 자기소개 필드를 갖도록 하려 한다.

참고로 현재 내가 작업하고 있는 프로젝트에는 `accounts` 라는 앱을 사용하고 있다. (없으면 `python manage.py startapp` 으로 `accounts`를 추가하자)

{% raw %} 

### accounts/models.py

```python
from django.db import models
from django.contrib.auth.models import (
    BaseUserManager, AbstractBaseUser
)


class MyUserManager(BaseUserManager):
    def create_user(self, email, nickname, info, password=None):
        if not email:
            raise ValueError('Users must have an email address')

        user = self.model(
            email=self.normalize_email(email),
            nickname=nickname,
            info=info,
        )

        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, nickname, info, password):
        user = self.create_user(
            email,
            password=password,
            nickname=nickname,
            info=info,
        )
        user.is_admin = True
        user.save(using=self._db)
        return user


class MyUser(AbstractBaseUser):
    email = models.EmailField(
        verbose_name='email',
        max_length=255,
        unique=True,
    )
    nickname = models.CharField(
        u'nickname',
        max_length=10,
        blank=False,
        unique=True,
        default='')
    info = models.CharField(
        u'info',
        max_length=400,
        blank=True,
        default=''
    )
    is_active = models.BooleanField(default=True)
    is_admin = models.BooleanField(default=False)

    objects = MyUserManager()

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = ['nickname', 'info']

    def get_full_name(self):
        return self.email

    def get_short_name(self):
        return self.email

    def __str__(self):
        return self.email

    def has_perm(self, perm, obj=None):
        "Does the user have a specific permission?"
        # Simplest possible answer: Yes, always
        return True

    def has_module_perms(self, app_label):
        "Does the user have permissions to view the app `app_label`?"
        # Simplest possible answer: Yes, always
        return True

    @property
    def is_staff(self):
        "Is the user a member of staff?"
        # Simplest possible answer: All admins are staff
        return self.is_admin
```

### accounts/admin.py

```python
from django import forms
from django.contrib import admin
from django.contrib.auth.models import Group
from django.contrib.auth.admin import UserAdmin as BaseUserAdmin
from django.contrib.auth.forms import ReadOnlyPasswordHashField

from .models import MyUser


class UserCreationForm(forms.ModelForm):
    """A form for creating new users. Includes all the required
    fields, plus a repeated password."""
    password1 = forms.CharField(label='Password', widget=forms.PasswordInput)
    password2 = forms.CharField(label='Password confirmation', widget=forms.PasswordInput)

    class Meta:
        model = MyUser
        fields = ('email', 'nickname', 'info')

    def clean_password2(self):
        # Check that the two password entries match
        password1 = self.cleaned_data.get("password1")
        password2 = self.cleaned_data.get("password2")
        if password1 and password2 and password1 != password2:
            raise forms.ValidationError("Passwords don't match")
        return password2

    def save(self, commit=True):
        # Save the provided password in hashed format
        user = super(UserCreationForm, self).save(commit=False)
        user.set_password(self.cleaned_data["password1"])
        if commit:
            user.save()
        return user


class UserChangeForm(forms.ModelForm):
    """A form for updating users. Includes all the fields on
    the user, but replaces the password field with admin's
    password hash display field.
    """
    password = ReadOnlyPasswordHashField()

    class Meta:
        model = MyUser
        fields = ('email', 'password', 'nickname', 'info', 'is_active', 'is_admin')

    def clean_password(self):
        # Regardless of what the user provides, return the initial value.
        # This is done here, rather than on the field, because the
        # field does not have access to the initial value
        return self.initial["password"]


class UserAdmin(BaseUserAdmin):
    # The forms to add and change user instances
    form = UserChangeForm
    add_form = UserCreationForm

    # The fields to be used in displaying the User model.
    # These override the definitions on the base UserAdmin
    # that reference specific fields on auth.User.
    list_display = ('email', 'nickname', 'info', 'is_admin')
    list_filter = ('is_admin',)
    fieldsets = (
        (None, {'fields': ('email', 'password')}),
        ('Personal info', {'fields': ('nickname', 'info', )}),
        ('Permissions', {'fields': ('is_admin',)}),
    )
    # add_fieldsets is not a standard ModelAdmin attribute. UserAdmin
    # overrides get_fieldsets to use this attribute when creating a user.
    add_fieldsets = (
        (None, {
            'classes': ('wide',),
            'fields': ('email', 'nickname', 'info', 'password1', 'password2')}
        ),
    )
    search_fields = ('email',)
    ordering = ('email',)
    filter_horizontal = ()

# Now register the new UserAdmin...
admin.site.register(MyUser, UserAdmin)
# ... and, since we're not using Django's built-in permissions,
# unregister the Group model from admin.
admin.site.unregister(Group)
```

### setting.py

setting.py에 다음을 추가한다.

```python
AUTH_USER_MODEL = 'accounts.MyUser'
```

# Usage

다음은 위에서 만든 유저 모델로 로그인을 하는 코드이다.

### accounts/urls.py

```python
from django.conf.urls import url, include
from django.contrib.auth import views as auth_views
from .views import LoginView, ProfileView

urlpatterns = [
    url(r'^login/$', LoginView.as_view(), name='login'),
    url(r'^logout/$', auth_views.logout, {'next_page': '/'}, name='logout'),
    url(r'^profile/$', ProfileView.as_view(), name='profile'),
]
```

### accounts/login.html

```python
{% extends "base_page.html" %}

{% block title %}login.html{% endblock %}

{% block content %}
    <form class="ui form" method="post">{% csrf_token %}
        <div class="field">
            <label>이메일</label>
            {{ form.email }}
        </div>
        <div class="field">
            <label>비밀번호</label>
            {{ form.password }}
        </div>
        <button class="ui submit button" type="submit">로그인</button>
        <input type="hidden" name="next" value="{{ next }}" />
    </form>
{% endblock content %}
```

### accounts/forms.py

```python
from django import forms
from django.conf import settings
from django.forms.widgets import *


class AuthenticationForm(forms.Form):
    email = forms.CharField(
        widget=TextInput(
            attrs={
                'placeholder': '이메일',
                'required': 'True',
            }
        )
    )
    password = forms.CharField(
        widget=PasswordInput(
            attrs={
                'placeholder': '비밀번호',
                'required': 'True',
            }
        )
    )

    class Meta:
        model = settings.AUTH_USER_MODEL
        fields = ['email', 'password']

```

`settings.AUTH_USER_MODEL` 을 통해 내가 만든 유저 모델에 접근할 수 있다.


### accounts/view.py

```python
from django.views.generic import FormView, TemplateView
from django.contrib.auth import authenticate, login, logout, REDIRECT_FIELD_NAME
from django.utils.decorators import method_decorator
from django.views.decorators.cache import never_cache
from django.views.decorators.csrf import csrf_protect
from django.views.decorators.debug import sensitive_post_parameters
from .forms import AuthenticationForm


class LoginView(FormView):
    template_name = 'accounts/login.html'
    success_url = '/accounts/profile'
    form_class = AuthenticationForm
    redirect_field_name = REDIRECT_FIELD_NAME

    @method_decorator(sensitive_post_parameters('password'))
    @method_decorator(csrf_protect)
    @method_decorator(never_cache)
    def dispatch(self, request, *args, **kwargs):
        # Sets a test cookie to make sure the user has cookies enabled
        request.session.set_test_cookie()

        return super(LoginView, self).dispatch(request, *args, **kwargs)

    def form_valid(self, form):
        user = authenticate(email=self.request.POST['email'], password=self.request.POST['password'])
        login(self.request, user)

        # If the test cookie worked, go ahead and
        # delete it since its no longer needed
        if self.request.session.test_cookie_worked():
            self.request.session.delete_test_cookie()

        return super(LoginView, self).form_valid(form)


class ProfileView(TemplateView):
    template_name = 'accounts/profile.html'

```

{% endraw %}
---
참고링크

- [Customizing authentication in Django](https://docs.djangoproject.com/en/dev/topics/auth/customizing/#a-full-example)
