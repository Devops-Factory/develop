//groovy script
def buildGithubCheck(repository, commitID, privateKey, status, checkName) {
    def currentTime = new Date().format("yyyy-MM-dd'T'HH:mm:ss'Z'")
    def checkName_run_id
  
    jsonWebToken = getJsonWebToken(privateKey)
    getStatusCode = validateAuth(jsonWebToken)
    if (!(getStatusCode in [200,201])) {
        error "Authentication request failed, status code: ${getStatusCode}"
    }
    token = getToken(jsonWebToken)
  
    try {
        checkName_run_id = getPreviousCheckNameRunID(repository, commitID, token, checkName)
    } catch(Exception e) {
        echo "Exception: ${e}"
        echo "Check name does not exist"
    }

    if (checkName_run_id) {
        getStatusCode = setCheckName(repository, checkName, status, currentTime, "PATCH", commitID, checkName_run_id)
    } else {
        getStatusCode = setCheckName(repository, checkName, status, previousDay, "POST", commitID)
    }
    if (!(getStatusCode in [200,201])) {
        error "Failed to create a check run, status code: ${getStatusCode}"
    }
}

// Custom HTTP request method
def setRequestMethod(HttpURLConnection c, String requestMethod) {
    try {
        final Object target;
        if (c instanceof HttpsURLConnectionImpl) {
            final Field delegate = HttpsURLConnectionImpl.class.getDeclaredField("delegate");
            delegate.setAccessible(true);
            target = delegate.get(c);
        } else {
            target = c;
        }
        final Field f = HttpURLConnection.class.getDeclaredField("method");
        f.setAccessible(true);
        f.set(target, requestMethod);
    } catch (IllegalAccessException | NoSuchFieldException ex) {
        throw new AssertionError(ex);
    }
}
