---
title: ViewSet
date: 2025-03-25 15:07:41
tags:
---


```python
class UserList(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer

class UserDetail(generics.RetrieveAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```
使用視圖集後

```python

class UserViewSet(viewsets.ReadOnlyModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```




```python
class SnippetList(generics.ListCreateAPIView):   
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]

    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)

class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly,
                      IsOwnerOrReadOnly]

class SnippetHighlight(generics.GenericAPIView):
    queryset = Snippet.objects.all()
    renderer_classes = [renderers.StaticHTMLRenderer]
    def get(self, request, *args, **kwargs):
        snippet = self.get_object()
        return Response(snippet.highlighted)
```

使用視圖集後


```python
class SnippetViewSet(viewsets.ModelViewSet):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
    permission_class = [premissions.IsAuthenticatedOrReadOnly, IsOwnerOrReadOnly]

    @action(detail = true, renderer_class = [renderers.StaticHTMLRenderer] )
    def highlight(self , request . *args , **kwargs):
        snippet = self.get_object()
        return Response(snippets.highlight)

    def perform_create(self,serializer):
        serializer.save(owner = self.request.user)
```
