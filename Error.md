
This error is happening on certain law firm ids. 
Im wondering if this happens when a law firm does not have a username set.

This is an example request that fails in Insomnia
https://api.lawbrokr.ca/v1/legacy/admin/account?law_firm_id=286

  + Exception Group Traceback (most recent call last):
  |   File "/app/.venv/lib/python3.13/site-packages/starlette/_utils.py", line 81, in collapse_excgroups
  |     yield
  |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 192, in __call__
  |     async with anyio.create_task_group() as task_group:
  |                ~~~~~~~~~~~~~~~~~~~~~~~^^
  |   File "/app/.venv/lib/python3.13/site-packages/anyio/_backends/_asyncio.py", line 783, in __aexit__
  |     raise BaseExceptionGroup(
  |         "unhandled errors in a TaskGroup", self._exceptions
  |     ) from None
  | ExceptionGroup: unhandled errors in a TaskGroup (1 sub-exception)
  +-+---------------- 1 ----------------
    | Traceback (most recent call last):
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/errors.py", line 164, in __call__
    |     await self.app(scope, receive, _send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/cors.py", line 87, in __call__
    |     await self.app(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 191, in __call__
    |     with recv_stream, send_stream, collapse_excgroups():
    |                                    ~~~~~~~~~~~~~~~~~~^^
    |   File "/usr/local/lib/python3.13/contextlib.py", line 162, in __exit__
    |     self.gen.throw(value)
    |     ~~~~~~~~~~~~~~^^^^^^^
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/_utils.py", line 87, in collapse_excgroups
    |     raise exc
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 193, in __call__
    |     response = await self.dispatch_func(request, call_next)
    |                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    |   File "/app/app/core/middleware.py", line 45, in dispatch
    |     response = await call_next(request)
    |                ^^^^^^^^^^^^^^^^^^^^^^^^
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 168, in call_next
    |     raise app_exc from app_exc.__cause__ or app_exc.__context__
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 144, in coro
    |     await self.app(scope, receive_or_disconnect, send_no_error)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 191, in __call__
    |     with recv_stream, send_stream, collapse_excgroups():
    |                                    ~~~~~~~~~~~~~~~~~~^^
    |   File "/usr/local/lib/python3.13/contextlib.py", line 162, in __exit__
    |     self.gen.throw(value)
    |     ~~~~~~~~~~~~~~^^^^^^^
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/_utils.py", line 87, in collapse_excgroups
    |     raise exc
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 193, in __call__
    |     response = await self.dispatch_func(request, call_next)
    |                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    |   File "/app/app/core/middleware.py", line 70, in dispatch
    |     response = await call_next(request)
    |                ^^^^^^^^^^^^^^^^^^^^^^^^
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 168, in call_next
    |     raise app_exc from app_exc.__cause__ or app_exc.__context__
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 144, in coro
    |     await self.app(scope, receive_or_disconnect, send_no_error)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/exceptions.py", line 63, in __call__
    |     await wrap_app_handling_exceptions(self.app, conn)(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 53, in wrapped_app
    |     raise exc
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 42, in wrapped_app
    |     await app(scope, receive, sender)
    |   File "/app/.venv/lib/python3.13/site-packages/fastapi/middleware/asyncexitstack.py", line 18, in __call__
    |     await self.app(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/routing.py", line 716, in __call__
    |     await self.middleware_stack(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/routing.py", line 736, in app
    |     await route.handle(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/routing.py", line 290, in handle
    |     await self.app(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 121, in app
    |     await wrap_app_handling_exceptions(app, request)(scope, receive, send)
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 53, in wrapped_app
    |     raise exc
    |   File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 42, in wrapped_app
    |     await app(scope, receive, sender)
    |   File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 107, in app
    |     response = await f(request)
    |                ^^^^^^^^^^^^^^^^
    |   File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 426, in app
    |     raw_response = await run_endpoint_function(
    |                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    |     ...<3 lines>...
    |     )
    |     ^
    |   File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 314, in run_endpoint_function
    |     return await dependant.call(**values)
    |            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    |   File "/app/app/legacy/routes/admin.py", line 182, in admin_account_detail
    |     return await get_admin_account_detail(db, law_firm_id)
    |            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    |   File "/app/app/legacy/services/admin.py", line 435, in get_admin_account_detail
    |     return AdminAccountDetailResponse(
    |         id=firm_id,
    |     ...<17 lines>...
    |         features=row["features"],  # pyright: ignore[reportAny] -- SQL row
    |     )
    |   File "/app/.venv/lib/python3.13/site-packages/pydantic/main.py", line 250, in __init__
    |     validated_self = self.__pydantic_validator__.validate_python(data, self_instance=self)
    | pydantic_core._pydantic_core.ValidationError: 1 validation error for AdminAccountDetailResponse
    | username
    |   Input should be a valid string [type=string_type, input_value=None, input_type=NoneType]
    |     For further information visit https://errors.pydantic.dev/2.12/v/string_type
    +------------------------------------

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/errors.py", line 164, in __call__
    await self.app(scope, receive, _send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/cors.py", line 87, in __call__
    await self.app(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 191, in __call__
    with recv_stream, send_stream, collapse_excgroups():
                                   ~~~~~~~~~~~~~~~~~~^^
  File "/usr/local/lib/python3.13/contextlib.py", line 162, in __exit__
    self.gen.throw(value)
    ~~~~~~~~~~~~~~^^^^^^^
  File "/app/.venv/lib/python3.13/site-packages/starlette/_utils.py", line 87, in collapse_excgroups
    raise exc
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 193, in __call__
    response = await self.dispatch_func(request, call_next)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/app/app/core/middleware.py", line 45, in dispatch
    response = await call_next(request)
               ^^^^^^^^^^^^^^^^^^^^^^^^
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 168, in call_next
    raise app_exc from app_exc.__cause__ or app_exc.__context__
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 144, in coro
    await self.app(scope, receive_or_disconnect, send_no_error)
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 191, in __call__
    with recv_stream, send_stream, collapse_excgroups():
                                   ~~~~~~~~~~~~~~~~~~^^
  File "/usr/local/lib/python3.13/contextlib.py", line 162, in __exit__
    self.gen.throw(value)
    ~~~~~~~~~~~~~~^^^^^^^
  File "/app/.venv/lib/python3.13/site-packages/starlette/_utils.py", line 87, in collapse_excgroups
    raise exc
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 193, in __call__
    response = await self.dispatch_func(request, call_next)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/app/app/core/middleware.py", line 70, in dispatch
    response = await call_next(request)
               ^^^^^^^^^^^^^^^^^^^^^^^^
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 168, in call_next
    raise app_exc from app_exc.__cause__ or app_exc.__context__
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/base.py", line 144, in coro
    await self.app(scope, receive_or_disconnect, send_no_error)
  File "/app/.venv/lib/python3.13/site-packages/starlette/middleware/exceptions.py", line 63, in __call__
    await wrap_app_handling_exceptions(self.app, conn)(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 53, in wrapped_app
    raise exc
  File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 42, in wrapped_app
    await app(scope, receive, sender)
  File "/app/.venv/lib/python3.13/site-packages/fastapi/middleware/asyncexitstack.py", line 18, in __call__
    await self.app(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/routing.py", line 716, in __call__
    await self.middleware_stack(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/routing.py", line 736, in app
    await route.handle(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/routing.py", line 290, in handle
    await self.app(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 121, in app
    await wrap_app_handling_exceptions(app, request)(scope, receive, send)
  File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 53, in wrapped_app
    raise exc
  File "/app/.venv/lib/python3.13/site-packages/starlette/_exception_handler.py", line 42, in wrapped_app
    await app(scope
    , receive, sender)
  File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 107, in app
    response = await f(request)
               ^^^^^^^^^^^^^^^^
  File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 426, in app
    raw_response = await run_endpoint_function(
                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ...<3 lines>...
    )
    ^
  File "/app/.venv/lib/python3.13/site-packages/fastapi/routing.py", line 314, in run_endpoint_function
    return await dependant.call(**values)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/app/app/legacy/routes/admin.py", line 182, in admin_account_detail
    return await get_admin_account_detail(db, law_firm_id)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/app/app/legacy/services/admin.py", line 435, in get_admin_account_detail
    return AdminAccountDetailResponse(
        id=firm_id,
    ...<17 lines>...
        features=row["features"],  # pyright: ignore[reportAny] -- SQL row
    )
  File "/app/.venv/lib/python3.13/site-packages/pydantic/main.py", line 250, in __init__
    validated_self = self.__pydantic_validator__.validate_python(data, self_instance=self)
pydantic_core._pydantic_core.ValidationError: 1 validation error for AdminAccountDetailResponse
username
  Input should be a valid string [type=string_type, input_value=None, input_type=NoneType]
    For further information visit https://errors.pydantic.dev/2.12/v/string_type



