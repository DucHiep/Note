# Note
--------------------------------------------
privateRouter đơn giản

```
const ProtectedRoute = ({ isAdmin, component: Component, ...rest }) => {
  const { loading, isAuthenticated, user } = useSelector((state) => state.user);

  return (
    <Fragment>
      {loading === false && (
        <Route
          {...rest}
          render={(props) => {
            if (isAuthenticated === false) {
              return <Redirect to="/login" />;
            }

            if (isAdmin === true && user.role !== "admin") {
              return <Redirect to="/login" />;
            }

            return <Component {...props} />;
          }}
        />
      )}
    </Fragment>
  );
};

export default ProtectedRoute;
```

http axios 
```
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from "axios";

const { REACT_APP_BASE_API } = process.env
enum StatusCode {
    Unauthorized = 401,
    Forbidden = 403,
    TooManyRequests = 429,
    InternalServerError = 500,
}

const headers: Readonly<Record<string, string | boolean>> = {
    Accept: "application/json",
    "Content-Type": "application/json; charset=utf-8",
    "Access-Control-Allow-Credentials": true,
    "X-Requested-With": "XMLHttpRequest",
};

// We can use the following function to inject the JWT token through an interceptor
// We get the `accessToken` from the localStorage that we set when we authenticate
const injectToken = (config: AxiosRequestConfig) => {
    try {
        const token = localStorage.getItem("accessToken");

        if (token != null) {
            config.headers = {
                ...headers,
                Authorization: `Bearer ${token}`
            };
        }
        return config;
    } catch (error) {
        return Promise.reject(error);
    }
};

class Http {
    private instance: AxiosInstance | null = null;

    private get http(): AxiosInstance {
        return this.instance != null ? this.instance : this.initHttp();
    }

    initHttp() {
        const http = axios.create({
            baseURL: REACT_APP_BASE_API,
            headers,
            withCredentials: true,
        });

        http.interceptors.request.use(injectToken, (error) => Promise.reject(error));

        http.interceptors.response.use(
            (response) => response,
            (error) => {
                const { response } = error;
                return this.handleError(response);
            }
        );

        this.instance = http;
        return http;
    }

    request<T = any, R = AxiosResponse<T>>(config: AxiosRequestConfig): Promise<R> {
        return this.http.request(config);
    }

    get<T = any, R = AxiosResponse<T>>(url: string, config?: AxiosRequestConfig): Promise<R> {
        return this.http.get<T, R>(url, config);
    }

    post<T = any, R = AxiosResponse<T>>(
        url: string,
        data?: T,
        config?: AxiosRequestConfig
    ): Promise<R> {
        return this.http.post<T, R>(url, data, config);
    }

    put<T = any, R = AxiosResponse<T>>(
        url: string,
        data?: T,
        config?: AxiosRequestConfig
    ): Promise<R> {
        return this.http.put<T, R>(url, data, config);
    }

    delete<T = any, R = AxiosResponse<T>>(url: string, config?: AxiosRequestConfig): Promise<R> {
        return this.http.delete<T, R>(url, config);
    }

    // Handle global app errors
    // We can handle generic app errors depending on the status code
    private handleError(error: { status: any; }) {
        const { status } = error;

        switch (status) {
            case StatusCode.InternalServerError: {
                // Handle InternalServerError
                break;
            }
            case StatusCode.Forbidden: {
                // Handle Forbidden
                break;
            }
            case StatusCode.Unauthorized: {
                // Handle Unauthorized
                break;
            }
            case StatusCode.TooManyRequests: {
                // Handle TooManyRequests
                break;
            }
        }

        return Promise.reject(error);
    }
}

export const http = new Http();
```
-----------------------------
# Cấu trúc thư mục
public: Folder này của React tạo ra, mình không bàn tới nhé
@types: Chứa các file định nghĩa interface, type cho Typescript
apis: Hay còn gọi là services. Chứa các khai báo function get api như axios,…
App: Chứa component App
assets: Chứa ảnh, videos, file, …
components: Chứa các folder component, trong mỗi folder là các file component
constansts: Chứa các các hằng số, enum
guards: Chứa các Route cần quyền truy cập
helpers: Chứa các function hay dùng, lặp đi lặp lại
hooks: Chứa các hook đang dùng
layouts: Chứa layout của dự án
pages: Chứa các trang của dự án
reducer: Chứa file reducer
routes: Chứa các route
store: chứa file store
------------------
