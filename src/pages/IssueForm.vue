<template>
  <div class="issue-form-container">
    <h2 class="title">{{ isEditMode ? '이슈 수정' : '새 이슈 등록' }}</h2>

    <form @submit.prevent="saveIssue" class="issue-form">
      <div class="form-group">
        <label for="title" class="form-label">제목:</label>
        <input id="title" v-model="form.title" required class="form-input" />
      </div>

      <div class="form-group">
        <label for="description" class="form-label">설명:</label>
        <textarea id="description" v-model="form.description" required class="form-textarea"></textarea>
      </div>

      <div class="form-group">
        <label for="status" class="form-label">상태:</label>
        <select id="status" v-model="form.status" required class="form-select">
          <option value="PENDING">대기 중</option>
          <option value="IN_PROGRESS">진행 중</option>
          <option value="COMPLETED">완료됨</option>
          <option value="CANCELLED">취소됨</option>
        </select>
      </div>

      <div class="form-group">
        <label for="user" class="form-label">담당자:</label>
        <select id="user" v-model="form.userId" class="form-select">
          <option disabled value="">선택하세요</option>
          <option v-for="user in users" :key="user.id" :value="user.id">
            {{ user.name }}
          </option>
        </select>
      </div>

      <div class="form-actions">
        <button type="submit" class="btn btn-primary">저장</button>
        <button type="button" @click="goBack" class="btn btn-secondary">목록으로</button>
      </div>
    </form>
  </div>
</template>

<script>
import { users, issues } from '@/data/mockData'

export default {
  name: 'IssueForm',
  data() {
    return {
      users,
      form: {
        id: null,
        title: '',
        description: '',
        status: 'PENDING',
        userId: '',
        createdAt: '',
        updatedAt: '',
      },
    }
  },
  computed: {
    isEditMode() {
      return !!this.$route.params.id
    },
  },
  created() {
    if (this.isEditMode) {
      const issueId = Number(this.$route.params.id)
      const issue = issues.find(i => i.id === issueId)

      if (issue) {
        this.form = {
          id: issue.id,
          title: issue.title,
          description: issue.description,
          status: issue.status,
          userId: issue.user?.id || '',
          createdAt: issue.createdAt,
          updatedAt: issue.updatedAt,
        }
      } else {
        alert('해당 이슈를 찾을 수 없습니다.')
        this.goBack()
      }
    }
  },
  methods: {
    saveIssue() {
      const user = this.users.find(u => u.id === Number(this.form.userId)) || null
      const selectedStatus = this.form.status

      if (!user && selectedStatus !== 'PENDING') {
        alert('담당자가 없는 경우 상태는 "대기 중"만 선택할 수 있습니다.')
        return
      }

      if (this.isEditMode) {
        const index = issues.findIndex(i => i.id === this.form.id)
        if (index !== -1) {
          issues[index] = {
            ...issues[index],
            ...this.form,
            user: user,
            updatedAt: new Date().toISOString(),
          }
          alert('이슈가 수정되었습니다.')
        }
      } else {
        const status = user ? selectedStatus : 'PENDING'
        const newIssue = {
          ...this.form,
          id: Date.now(),
          status,
          user: user,
          createdAt: new Date().toISOString(),
          updatedAt: new Date().toISOString(),
        }
        issues.push(newIssue)
        alert('새 이슈가 등록되었습니다.')
      }

      this.goBack()
    },
    goBack() {
      this.$router.push('/issues')
    },
  },
}
</script>

<style scoped>
.issue-form-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 1.5rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #fafafa;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.title {
  text-align: center;
  margin-bottom: 1.5rem;
  color: #333;
  font-weight: 600;
  font-size: 1.8rem;
}

.issue-form {
  display: flex;
  flex-direction: column;
}

.form-group {
  margin-bottom: 1rem;
  display: flex;
  flex-direction: column;
}

.form-label {
  margin-bottom: 0.4rem;
  font-weight: 500;
  color: #555;
}

.form-input,
.form-textarea,
.form-select {
  padding: 0.5rem 0.75rem;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  transition: border-color 0.3s ease;
  font-family: inherit;
}

.form-input:focus,
.form-textarea:focus,
.form-select:focus {
  border-color: #3b82f6;
  outline: none;
  box-shadow: 0 0 5px rgba(59, 130, 246, 0.5);
}

.form-textarea {
  resize: vertical;
  min-height: 100px;
  font-family: inherit;
}

.form-actions {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 1rem;
}

.btn {
  cursor: pointer;
  padding: 0.5rem 1.2rem;
  font-size: 1rem;
  font-weight: 600;
  border: none;
  border-radius: 4px;
  color: white;
  transition: background-color 0.3s ease;
}

.btn-primary {
  background-color: #3b82f6;
}

.btn-primary:hover {
  background-color: #2563eb;
}

.btn-secondary {
  background-color: #6b7280;
}

.btn-secondary:hover {
  background-color: #4b5563;
}
</style>
