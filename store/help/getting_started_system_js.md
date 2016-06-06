# Getting Started With SystemJS and Angular CLI

1. Modify system-config.ts:
    ```ts
        /** Map relative paths to URLs. */
        const map: any = {
            '@ngrx': 'vendor/@ngrx'
        };
        
        /** User packages configuration. */
        const packages: any = {
            '@ngrx/core': {
                main: 'index.js',
                format: 'cjs'
            },
            '@ngrx/store': {
                main: 'index.js',
                format: 'cjs'
            }
        };
    ```

2. Modify angular-cli-build.js by adding this line to vendorNpmFiles:
    ```js
        '@ngrx/**/*.+(js|js.map)'
    ```