FROM node:22-alpine AS build

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

ARG VITE_USER_SERVICE_URL=/api/users
ARG VITE_STUDENT_SERVICE_URL=/api/students
ARG VITE_LECTURER_SERVICE_URL=/api/lecturers
ARG VITE_COURSE_SERVICE_URL=/api/courses
ARG VITE_ENROLLMENT_SERVICE_URL=/api/enrollments

ENV VITE_USER_SERVICE_URL=${VITE_USER_SERVICE_URL}
ENV VITE_STUDENT_SERVICE_URL=${VITE_STUDENT_SERVICE_URL}
ENV VITE_LECTURER_SERVICE_URL=${VITE_LECTURER_SERVICE_URL}
ENV VITE_COURSE_SERVICE_URL=${VITE_COURSE_SERVICE_URL}
ENV VITE_ENROLLMENT_SERVICE_URL=${VITE_ENROLLMENT_SERVICE_URL}

RUN npm run build

FROM nginx:1.27-alpine

# Security remediation (Task 10.2D): Docker Scout flagged critical/high CVEs
# in curl, openssl, libxml2 and expat -- Alpine OS packages baked into the
# upstream nginx:1.27-alpine image, not anything this app uses directly
# (nginx only serves the static build output). Rather than waiting for
# Docker Hub to rebuild the base image, pull the latest patched package
# versions from Alpine's own repositories at build time. --no-cache avoids
# leaving the apk index cached in the image layer.
RUN apk update && apk upgrade --no-cache

COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]