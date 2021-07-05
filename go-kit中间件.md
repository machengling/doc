```
func AuthMiddleware(logger *zap.Logger) endpoint.Middleware {
    return func(next endpoint.Endpoint) endpoint.Endpoint {
        return func(ctx context.Context, request interface{}) (response interface{}, err error) {
            token := fmt.Sprint(ctx.Value(utils.JWT_CONTEXT_KEY))
            if token == "" {
                err = errors.New("请登录")
                logger.Debug(fmt.Sprint(ctx.Value(v3_service.ContextReqUUid)),zap.Any("[AuthMiddleware]","token == empty"), zap.Error(err))
                return "", err
            }
            jwtInfo, err := utils.ParseToken(token)
            if err != nil {
                logger.Debug(fmt.Sprint(ctx.Value(v3_service.ContextReqUUid)),zap.Any("[AuthMiddleware]","ParseToken"), zap.Error(err))
                return "", err
            }
            if v, ok := jwtInfo["Name"]; ok {
                ctx = context.WithValue(ctx, "name", v)
            }
            return next(ctx, request)
        }
    }
}

```