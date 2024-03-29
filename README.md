## 실행 방법
1.  package.json에 정의되어 있는 패키지 설치 (최초 1회)
    ```shell
    $ yarn
    ```
1.  개발 모드로 서버 실행
    ```shell
    $ yarn dev
    ```
1.  프로젝트 빌드
    ```shell
    $ yarn build
    ```
1.  운영 모드로 서버 실행
    ```shell
    $ yarn start
    ```

## 패키지 설치 및 설정 순서

1.  TypeScript + Next.js 설치
    ```shell
    $ npx create-next-app@latest next-app --typescript
    ```
1.  Prettier 설치
    ```shell
    $ yarn add -D prettier eslint-plugin-prettier eslint-config-prettier
    ```
1.  Prettier관련 .eslintrc.json 수정
    ```json
    {
      "globals": {
        "JSX": true
      },
      "env": {
        "browser": true,
        "es2021": true,
        "node": true
      },
      "extends": [
        "eslint:recommended",
        "next",
        "next/core-web-vitals",
        "plugin:prettier/recommended"
      ],
      "rules": {
        "no-unused-vars": "off"
      }
    }
    ```
1.  Prettier관련 .prettierrc.json 생성
    ```json
    {
      "semi": true,
      "trailingComma": "es5",
      "singleQuote": true,
      "jsxSingleQuote": true,
      "tabWidth": 2,
      "useTabs": false
    }
    ```
1.  VSCode에서 Prettier Plugin 설치 후, setting.json 수정 (.prettier.json보다 Code Formatting 적용 우선순위 낮음)
    ```json
    {
      // ...
      // "editor.fontWeight": "normal",
      "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
      },
      "editor.formatOnSave": true,
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
    ```
1.  src 디렉토리 생성 및 각종 디렉토리 이동
    ```shell
    $ mkdir src && mv ./pages ./src/pages && mv ./styles ./src/styles
    ```
1.  유형별 디렉토리 생성
    ```shell
    $ cd src && mkdir assets components constants contexts helpers hooks i18n layouts styles services types utils
    ```
1.  절대경로관련 tsconfig.json 수정
    ```json
    {
      "compilerOptions": {
        // ...
        // "incremental": true,
        "baseUrl": ".",
        "paths": {
          "~assets/*": ["./src/assets/*"],
          "~components/*": ["./src/components/*"],
          "~constants/*": ["./src/constants/*"],
          "~contexts/*": ["./src/contexts/*"],
          "~helpers/*": ["./src/helpers/*"],
          "~hooks/*": ["./src/hooks/*"],
          "~i18n/*": ["./src/i18n/*"],
          "~layouts/*": ["./src/layouts/*"],
          "~pages/*": ["./src/pages/*"],
          "~styles/*": ["./src/styles/*"],
          "~services/*": ["./src/services/*"],
          "~types/*": ["./src/types/*"],
          "~utils/*": ["./src/utils/*"],
          "~public/*": ["./public/*"],
          "~src/*": ["./src/*"],
          "~/*": ["./*"]
        }
      }
      // ...
    }
    ```
1.  Emotion 설치
    ```shell
    $ yarn add @emotion/react @emotion/styled @emotion/css @emotion/server
    ```
1.  Emotion css props관련 tsconfig.json 수정
    ```json
    {
      "compilerOptions": {
        // ...
        // "jsx": "preserve",
        "jsxImportSource": "@emotion/react"
        // ...
      }
      // ...
    }
    ```
1.  Tailwind CSS 설치
    ```shell
    $ yarn add -D tailwindcss postcss autoprefixer
    ```
1.  tailwind.config.js 생성
    ```shell
    $ npx tailwindcss init --full
    ```
1.  postcss.config.js 생성
    ```javascript
    module.exports = {
      plugins: {
        tailwindcss: {},
        autoprefixer: {},
      },
    };
    ```
1.  Template 경로 설정을 위한 tailwind.config.js 수정
    ```javascript
    module.exports = {
      content: ['./src/**/*.{js,ts,jsx,tsx}'],
      // presets: [],
      // ...
    };
    ```
1.  Tailwind CSS 기본 스타일 적용을 위한 globals.css 수정
    ```css
    @tailwind base;
    @tailwind components;
    @tailwind utilities;
    ```
1.  Emotion와 Tailwind CSS의 연동을 위한 macro 설치
    ```shell
    $ yarn add -D twin.macro @emotion/babel-plugin babel-plugin-macros
    ```
1.  .babelrc.js 생성
    ```javascript
    module.exports = {
      presets: [
        [
          'next/babel',
          {
            'preset-react': {
              runtime: 'automatic',
              importSource: '@emotion/react',
            },
          },
        ],
      ],
      plugins: ['@emotion/babel-plugin', 'babel-plugin-macros'],
    };
    ```
1.  CORS 이슈 우회 설정을 위한 next.config.js 수정
    ```javascript
    const nextConfig = {
      // reactStrictMode: true,
      async rewrites() {
        return [
          {
            source: '/:path*',
            destination: 'http://{BACKEND_IP}:{BACKEND_PORT}/:path*',
          },
        ];
      },
    };
    ```
1.  SVGR 설정을 위한 file-loader, @svgr/webpack 설치
    ```shell
    $ yarn add file-loader
    $ yarn add -D @svgr/webpack
    ```
1.  SVGR 설정을 위한 next.config.js 수정
    ```javascript
    const nextConfig = {
      // ...
      // async rewrites() {
      //   // ...
      // },
      webpack: (config) => {
        config.module.rules.push({
          test: /\.svg$/,
          use: [
            {
              loader: '@svgr/webpack',
            },
            {
              loader: 'file-loader',
              options: {
                name: 'static/[path][name].[ext]',
              },
            },
          ],
          type: 'javascript/auto',
          issuer: {
            and: [/\.(ts|tsx|js|jsx|md|mdx)$/],
          },
        });
        return config;
      },
    };
    ```
1.  SVGR 설정을 위한 svg.d.ts 생성
    ```typescript
    declare module '*.svg; {
      import React = require('react');
      export const ReactComponent: React.FunctionComponent<
        React.SVGProps<SVGSVGElement>
      >;
      const src: string;
      export default src;
    }
    ```
1.  svg.d.ts 인식을 위한 tsconfig.json 수정
    ```json
    {
      // "compilerOptions": {
      //   // ...
      // },
      "include": [
        // ...
        // ".next/types/**/*.ts",
        "src/types/*.d.ts"
      ],
      // ...
    }
    ```
1.  Storybook 설치
    ```shell
    $ npx sb init --builder webpack5
    ```
1.  Storybook에 globals.css 적용 및 Next Images관련 .storybook/preview.js 수정
    ```javascript
    import '../src/styles/globals.css';
    import * as NextImage from 'next/image';

    const OriginalNextImage = NextImage.default;

    Object.defineProperty(NextImage, 'default', {
      configurable: true,
      value: (props) => <OriginalNextImage {...props} unoptimized />,
    });
    ```
1.  Storybook에 절대경로관련 설정을 위한 .storybook/main.js 수정
    ```javascript
    const path = require('path');
    const PROJECT_ROOT = process.cwd();
    const pathAlias = require('../tsconfig.json').compilerOptions.paths;
    
    module.exports = {
      // ...
      // addons: [
      //   // ...
      // ],
      webpackFinal: async (config) => {
        config.resolve.alias = {
          ...config.resolve.alias,
          ...Object.fromEntries(
            Object.entries(pathAlias).map(([key, valArr]) => [
              key.replace('/*', ''),
              valArr.map((val) =>
                path.resolve(
                  PROJECT_ROOT,
                  ...val.replace('/*', '').replace('./', '').split('/')
                )
              ),
            ])
          ),
        };
        return config;
      },
      // ...
    };
    ```
1.  Storybook에 보여줄 디렉토리 추가 및 public 디렉토리 제공을 위한 .storybook/main.js 수정
    ```javascript
    module.exports = {
      stories: [
        '../src/components/**/*.stories.@(js|jsx|ts|tsx|mdx)',
        '../src/layouts/**/*.stories.@(js|jsx|ts|tsx|mdx)',
        '../src/pages/**/*.stories.@(js|jsx|ts|tsx|mdx)',
        // '../src/**/*.stories.mdx',
        // ...
      ],
      staticDir: ['../public'],
      // ...
    };
    ```
1.  @emotion/babel-preset-css-prop 설치
    ```shell
    $ yarn add -D @emotion/babel-preset-css-prop
    ```
1.  @emotion/babel-preset-css-prop 설정을 위한 .storybook/main.js 수정
    ```javascript
    module.exports = {
      // ...
      // addons: [
      //   // ...
      // ],
      webpackFinal: async (config) => {
        // config.resolve.alias = {
        //   // ...
        // };
        config.module.rules.push({
          test: /\.(ts|tsx)$/,
          loader: require.resolve('babel-loader'),
          options: {
            presets: [require.resolve('@emotion/babel-preset-css-prop')],
          },
        });
        return config;
      },
      // ...
    };
    ```
1.  PostCSS addon 설치
    ```shell
    $ yarn add -D @storybook/addon-postcss
    ```
1.  PostCSS addon 설정을 위한 .storybook/main.js 수정
    ```javascript
    module.exports = {
      // ...
      // staticDir: ['../public'],
      addons: [
        // ...
        // '@storybook/addon-interactions',
        {
          name: '@storybook/addon-postcss',
          options: {
            postcssLoaderOptions: {
              implementation: require('postcss'),
            },
          },
        },
      ],
      // ...
    };
    ```
1.  Storybook에 SVGR 설정을 위한 .storybook/main.js 수정
    ```javascript
    module.exports = {
      // ...
      // addons: [
      //   // ...
      // ],
      webpackFinal: async (config) => {
        // ...
        // config.module.rules.push({
        //   // ...
        // });
        config.module.rules.push({
          test: /\.svg$/,
          use: [
            {
              loader: '@svgr/webpack',
            },
            {
              loader: 'file-loader',
              options: {
                name: 'static/[path][name].[ext]',
              },
            },
          ],
          type: 'javascript/auto',
          issuer: {
            and: [/\.(ts|tsx|js|jsx|md|mdx)$/],
          },
        });
        return config;
      },
      // ...
    };
    ```
